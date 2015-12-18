# spadesAnalytics-troianoMethod
Troiano method is implemented to identify wear and non-wear based on generated activity count.

Usage
-----
```ShellSession
java -cp [Local directory to jar file] [com.qmedic.spades.task.SpadesSubmit] [-cluster] [MapReduce] [-mode] [Once] [-speed] [32x] [-filesystem] [s3n] [-jar] [s3n directory to jar file] [-classpath] [class name in jar file of s3n] [-input] [data input path] [-key] [key value assigned by AWS] [-secret] [secret value assigned by AWS] [-p] [parameters to be passed to runnable class]
```
* [Local directory to jar file]: The local directory for jar file
   * Example:  output/SpadesAnalytics.jar (here SpadesAnalytics.jar is a runnable jar file in the local computer)
* [com.qmedic.spades.task.SpadesSubmit]: the class to submit the job to EMR
* [s3n directory to jar file]: The cloud directory for jar file
   * Example: spades-data/development/user/SpadesAnalytics.jar (SpadesAnalytics.jar is stored in the directory “spades-data/development/user” of s3)
* [class name in jar file of s3n]: The runnable class in the jar file in S3
   * Example: com.qmedic.spades.task.examples.mapreduce.WearAndNonWear.TroianoMethodClean(The class to identify wear and non-wear based on input data activity count)
* [data input path]: Cloud directory for source data
   * Note: Its format will be discussed in the section “Data Input Format”
   * Example: spades-data/development/stanford/2/StanfordStudyYouth2/1/MetaData-ActivityCountClean-2015-10-29-10-50-21-021/*
* [key value assigned by AWS]: Key value assigned by AWS
   * Note: 20  alphabeta
   * Example: "yourAccessKey"
* [secret value assigned by AWS]: secret value assigned by AWS
   * Note: 40 char
   * Example: "yourSecretKey"
* [parameters to be passed to runnable class]: parameters to be passed to runnable class
   * Note: Its format will be discussed in the section “Parameter Input Format”

Input
-----
In our framework, the input format for processing data is restricted to the following, a future work item would be to extend it and allow for a variety of flexible formats…

* Input Path Folder Format :
   *Must be terminated with a wild card /*
   * The path consists of the following required entries:
      * Bucket Name: (alphanumeric, ‘-’)... e.g. spade-data
      * Release Type: development, production
      * Institution: Stanford
      * Study ID: numeric value for the user within the institution
      * Study Name:  StanfordStudyYouth2
      * Participant ID: acceptable values include (can also be specified as a wild card *)
      * Data source: Alphanumeric + ‘-’...e.g. MasterSynced
   * Optional components of the path that follow include:
      * Year: 4 character representation
      * Month: 2 character representation that goes from 01 to 12
      * Day: 2 character representation that goes from 01 to 31
      * Hour: 2 character representation that goes from 00 to 23
   * Allowed paths include the following formats:
spades-data/development/stanford/2/StanfordStudyYouth2/1/MetaData-ActivityCountClean-2015-10-29-10-50-21-021/*

Parameter Input
---------------
The parameter input format for passing parameters to runnable class is restricted to the following, a future work item would be extended and allow for a variety of flexible formats.

**Format**
```ShellSession
-c [false] -f [s3n] -j [s3n directory to jar file] -a [class name in jar file of s3n] -i [data input path]
```

**Example**
```ShellSession
-c false -f s3n -j spades-data/development/user/SpadesAnalytics.jar -a com.qmedic.spades.task.examples.mapreduce.WearAndNonWear.TroianoMethodClean -i spades-data/development/stanford/2/StanfordStudyYouth2/1/MetaData-ActivityCountClean-2015-10-29-10-50-21-021/*
```
* [false]: Input value for [-c] command which doesn’t allow cross participants
   * Example: false/true
* [s3n]: Input value for [-f] command which runs in s3n file system
   * Example: s3n/hdfs
* [s3n directory to jar file]: Input jar directory in s3n for [-j] command which specifies the runnable jar file
   * Example: spades-data/development/user/SpadesAnalytics.jar
* [class name in jar file of s3n]: Input s3 class for [-a] command which specifies the runnable class 
   * Example: com.qmedic.spades.task.examples.mapreduce.WearAndNonWear.TroianoMethodClean
* [data input path]: Input source data for [-i] command which specifies the source data
   * Example: spades-data/development/stanford/2/StanfordStudyYouth2/1/MetaData-ActivityCountClean-2015-10-29-10-50-21-021/*
   * 

Command Line Example
--------------------
```ShellSession
java -cp output/SpadesAnalytics.jar com.qmedic.spades.task.SpadesSubmit -cluster MapReduce -mode Once -speed 32x -filesystem s3n -jar spades-data/development/user/SpadesAnalytics.jar -classpath com.qmedic.spades.task.examples.mapreduce.WearAndNonWear.TroianoMethodClean -input spades-data/development/stanford/2/StanfordStudyYouth2/1/MetaData-ActivityCountClean-2015-10-29-10-50-21-021/*   -key "yourAccessKey" -secret "yourSecretKey" -duration 4 -cost 20 -p "-c false -f s3n -j spades-data/development/user/SpadesAnalytics.jar -a com.qmedic.spades.task.examples.mapreduce.WearAndNonWear.TroianoMethodClean -i spades-data/development/stanford/2/StanfordStudyYouth2/1/MetaData-ActivityCountClean-2015-10-29-10-50-21-021/*
```

Output
------
Typically the feature data file will be stored in the metadata directories and will include processed data that can be recomputed from the raw data. The structure of the filename is:
```ShellSession
[ALGORITHM].[FEATURE NAME].[YYYY]-[MM]-[DD]-[hh]-[mm]-[ss]-[mmm]-[P/M][hhmm].csv.gz 
```
* [ALGORITHM]: Prefix of the file name
* [FEATURE NAME]: Feature name
   * Example: MetaData-TroianoMethodClean-2015-10-29-10-50-21-021

**Output Content**

For example, the first two lines in the activity count.csv file could look like this:
```ShellSession
HEADER_TIME_STAMP, START_TIME,END_TIME, WEAR/NON-WEAR,LENGTH(MINs)
2015-10-29 16:03:37.746, 2013/11/20 05:00:00.000,2013/11/20 05:59:59.999,Non-Wear,60
```
* [HEADER_TIME_STAMP]: The time to generate this output
* [START_TIME]: Start timestamp of Wear/Non-Wear 
* [END_TIME]: End timestamp of wear/non-wear
* [WEAR/NON-WEAR]: Wear or non-wear status
* [LENGTH(MINs)]: Duration of wear/non-wear status

Note
----
Here it is assumed that the accelerometer has three axis accelerations.

References
----------
[Troiano RP, Berrigan D, Dodd KW, Masse LC, Tilert T, McDowell M. Physical activity in the united states measured by accelerometer. Med Sci Sports Exerc. 2008;40(1):181–8](http://www.ncbi.nlm.nih.gov/pubmed/18091006)

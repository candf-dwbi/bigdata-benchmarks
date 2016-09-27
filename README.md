hive-testbench
==============

A testbench for Clients & Friends with Apache Hive, PIG, Spark at any data scale.
Based on Hortonworks Hive-testbench with instruction in README.md.hive-testbench

Quick start
==============
```

sudo yum -y install unzip gcc make flex bison byacc git
git clone https://github.com/candf-dwbi/bigdata-benchmarks
cd ./bigdata-benchmarks/generator
chmod 750 ./tpcds-build.sh
./tpcds-build.sh

# This will generate data:
# scale 10  - 10 GB fill dataset 
# scale 100 - 100 GB full dataset
chmod 750 ./tpcds-setup-candf-gen.sh
./tpcds-setup-candf-gen.sh 10 /benchmarks/tpcds 
./tpcds-setup-candf-gen.sh 100 /benchmarks/tpcds 


# This will create HIVE tables
cd ../hive
chmod 750 ./tpcds-setup-candf-hive.sh
./tpcds-setup-candf-hive.sh 10
./tpcds-setup-candf-hive.sh 100

# This will create PARQUET tables
chmod 750 ./tpcds-setup-format.sh
./tpcds-setup-format.sh 10
./tpcds-setup-format.sh 100



```

Overview
========

The hive-testbench is a data generator and set of queries that lets you experiment with Apache Hive at scale. The testbench allows you to experience base Hive performance on large datasets, and gives an easy way to see the impact of Hive tuning parameters and advanced settings.

Use instruction from Hortonworks Hive-testbench


This is based on 
It's an extension for Pig and Spark scripts

Prerequisites
=============

You will need:
* The same like Hortonworks Hive-testbench
   Use instruction from Hortonworks Hive-testbench
* Apache Hive, pig, Spark

Install and Setup
=================

All of these steps should be carried out on your Hadoop cluster.

- Step 0: Download
  
  cd /mapr/EUAESPROD3/user/mapr/BenchmarkScripts/
  git clone https://github.com/pfizer/AES-ENV.git
  move mapr/EUAESPROD3/user/mapr/BenchmarkScripts/AES-ENV/hive-testbench mapr/EUAESPROD3/user/mapr/BenchmarkScripts/
 
  
- Step 1: Prepare your environment.

  Use instruction from Hortonworks Hive-testbench
  
- Step 2: Decide which test suite(s) you want to use.

  Use instruction from Hortonworks Hive-testbench

- Step 3: Compile and package the appropriate data generator.
  ssh to Hadoop Mapr cluster node eg: 
  ssh mapr@euw1z1tl001
  cd /mapr/EUAESPROD3/user/mapr/BenchmarkScripts/hive-testbench
  For TPC-DS, ```./tpcds-build.sh``` downloads, compiles and packages the TPC-DS data generator.
  For TPC-H, ```./tpch-build.sh``` downloads, compiles and packages the TPC-H data generator.

- Step 4: Decide how much data you want to generate.

 Use instruction from Hortonworks Hive-testbench


- Step 5: Generate and load the data to HIVE.

  The scripts ```tpcds-setup.sh``` and ```tpch-setup.sh``` generate and load data for TPC-DS and TPC-H, respectively. General usage is ```tpcds-setup.sh scale_factor [directory]``` or ```tpch-setup.sh scale_factor [directory]```

  For C&F data for all steps (generate data, map in hive text, generate ocr in hive) use:
      tpcds-setup-candf.sh 
  For C&F data generation split to 3 phase:
   Generate data on hdf use:  
    tpcds-setup-candf-gen.sh
   Map text files in hive tables:
    tpcds-setup-candf-hive.sh     
   Generate OCR tables in hive use:
    tpcds-setup-candf-orc.sh   

  Some examples:

  Build 1 TB of TPC-DS data: ```./tpcds-setup-candf.sh 1000```

   
  Build 100 TB of TPC-DS data: ```./tpcds-setup-candf.sh 100000```

  Build 1 TB of text formatted TPC-DS data: ```FORMAT=textfile ./tpcds-setup-candf.sh 1000```

  Build 30 TB of RCFile formatted TPC-DS data: ```FORMAT=rcfile ./tpcds-setup-candf.sh 30000```

- Step 6: Run queries.

 Use Nodes with :
  node with PIG = euw1z1tl001
  node with HIVE = euw1z1tl002
  node with Spark = euw1z1tl001
  User: mapr

  RUN Hive
    ssh mapr@euw1z1tl002 
    cd installation_dir eg: cd /mapr/EUAESPROD3/user/mapr/BenchmarkScripts/hive-testbench

    ./run_hive_CandF.pl candf 20

   RUN Spark
    ssh mapr@euw1z1tl001 
    cd installation_dir eg: cd /mapr/EUAESPROD3/user/mapr/BenchmarkScripts/hive-testbench

    ./run_pySpark_CandF.sh 20
    
   RUN PIG
    
    ssh mapr@euw1z1tl001 
    cd installation_dir eg: cd /mapr/EUAESPROD3/user/mapr/BenchmarkScripts/hive-testbench
    ./run_pig_CandF.sh 20
      
      Execution time run_pig 20 1 1 285.840887061 seconds
    
   
   
Feedback
 

Execution times:

Cluster 5Nodes
  PIG 20 & 300
    Execution time run_pig 20 1 1 285.840887061
    Execution time run_pig 300 1 1 1587.061531001 seconds
  HIVE 20 Run1
    join.sql,success,42,100
    queryofdeath.sql,success,114,100
  HIVE 20 Run2
    join.sql,success,22,100
    queryofdeath.sql,success,61,100
  HIVE 300  Run1
    join.sql,success,306,100
    queryofdeath.sql,success,416,100
  Spark 20
   Execution time run_py 20 428.136100413 seconds
  Spark  300 
   To much errors , Spark filed 
   
  
Pig additional information from logs
 Get detailed execution time
      
      cat  ./candf-pig/scripts/run_pig20160325_1458911472.log
  
        Running Pig Query join.pig
        Going to run /opt/mapr/pig/pig-0.14//bin/pig  -param input=/benchmarks/tpcds-generate/20 -param output=/benchmarks/tpcds-generate/20/pig-out/ -param factor=20 -f join.pig
        join.pig times (sec):   Pig     136

        processing queryOfdeath.pig file ..

        Running Pig Query queryOfdeath.pig
        Going to run /opt/mapr/pig/pig-0.14//bin/pig  -param input=/benchmarks/tpcds-generate/20 -param output=/benchmarks/tpcds-generate/20/pig-out/ -param factor=20 -f queryOfdeath.pig
        queryOfdeath.pig times (sec):   Pig     146

        Total times (sec):      Pig     282
 

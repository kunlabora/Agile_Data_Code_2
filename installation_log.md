# SETUP 

```git clone https://github.com/rjurney/Agile_Data_Code_2.git```

Install vagrant: https://www.vagrantup.com/downloads.html

Install virtual box: https://www.virtualbox.org/wiki/Downloads
(requires 2 installation attempts to allow special permissions when the 1st attempt fails. If someone knows why this special permission is needed, I d be interessted to hear (Bruno))

Adapt vagrant file:
* https://github.com/viveksatasiya/vagrant-prometheus-grafana/issues/1
* Spark has moved its tar:
    ```
    curl -Lko /tmp/spark-2.2.1-bin-without-hadoop.tgz https://archive.apache.org/dist/spark/spark-2.2.1/spark-2.2.1-bin-hadoop2.7.tgz
    ```

```
vagrant up
```
(Took 30min)

```
vagrant ssh
cd Agile_Data_Code_2/
./download.sh
```

# Demo jupyter: 
removed support 5 Apr 2018
# Demo jsonl: check
```
ls -lah /tmp/
```
no test.jsonl file
```
cd Agile_Data_Code_2/
python ch02/test_json.py
ls -lah /tmp/
cat /tmp/test.jsonl
```
This should show some test data

# Demo Spark: check (after fixing Vagrant file, for location of Spark tar)
```
pyspark
csv_lines = sc.textFile("data/example.csv")
data = csv_lines.map(lambda line: line.split(","))
data.collect()
```
This should show some test data

To leave the Spark terminal and return to the bash terminal: ```exit()```

# Demo MongoDB: 
Direct access: check
```
mongo agile_data_science
db.my_collection.insert({"name": "Russel Jurney"});
db.my_collection.find({"name": "Russel Jurney"});
```
This should show the example you inserted

To leave Mongo terminal and return to the bash terminal: ```exit```

From PySpark:
* Running script:
    ```
    spark-submit ch02/pyspark_mongodb.py
    ```
    Failure due to: sc (SparkContext) is not imported and or created
* Interactive:
        1. (Optional?) sudo pip install pyspark
    ```
    pyspark --jars ./lib/mongo-hadoop-spark-2.0.2.jar,./lib/mongo-java-driver-3.4.2.jar,./lib/mongo-hadoop-2.0.2.jar --driver-class-path ./lib/mongo-hadoop-spark-2.0.2.jar:./lib/mongo-java-driver-3.4.2.jar:./lib/mongo-hadoop-2.0.2.jar
    ```
    Copy paste the python code into the interactive PySpark shell:
    ```
    import pymongo
    import pymongo_spark
    # Important: activate pymongo_spark.
    pymongo_spark.activate()

    csv_lines = sc.textFile("data/example.csv")
    data = csv_lines.map(lambda line: line.split(","))
    schema_data = data.map(lambda x: {'name': x[0], 'company': x[1], 'title': x[2]})
    schema_data.saveToMongoDB('mongodb://localhost:27017/agile_data_science.executives')
    ```
    Leave the PySpark terminal: ```exit()```
    ```
    mongo agile_data_science
    db.executives.find()
    ```
    This should show some test data
    Leave the Mongo terminal: ```exit```

# WIP

* Demo elastic search:
    1) Run this curl statement in your bash terminal
        ```
        curl -XPUT 'localhost:9200/agile_data_science?pretty' -H 'Content-Type: application/json' -d'{"settings": {"index": {"number_of_shards": 1, "number_of_replicas":1}}}'
        ```
        This should show some acknowledgment:
        ```
        {
            "acknowledged" : true,
            "shards_acknowledged" : true
        }
        ```
    2) Run this curl statement in your bash terminal
        ```
        curl -XPUT 'localhost:9200/agile_data_science/test/1?pretty' -H 'Content-Type: application/json' -d'{"name": "Russel Jurney", "message": "trying out Elasticsearch"}'
        ```
        This shows an error:
        ```
        {
            "error" : {
                "root_cause" : [
                    {
                        "type" : "mapper_parsing_exception",
                        "reason" : "failed to parse"
                    }
                ],
                "type" : "mapper_parsing_exception",
                "reason" : "failed to parse",
                "caused_by" : {
                    "type" : "not_x_content_exception",
                    "reason" : "Compressor detection can only be called on some xcontent bytes or compressed xcontent bytes"
                }
            },
            "status" : 400
        }
        curl: (6) Couldn't resolve host '“Russel'
        curl: (6) Couldn't resolve host 'Jurney”,'
        curl: (6) Couldn't resolve host '“message”'
        curl: (6) Couldn't resolve host '“trying'
        curl: (6) Couldn't resolve host 'out'
        curl: (3) [globbing] unmatched close brace/bracket at pos 17
        ```



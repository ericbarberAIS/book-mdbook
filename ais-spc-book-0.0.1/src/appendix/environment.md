### Spark Setup
```bash
sudo apt update
wget https://dlcdn.apache.org/spark/spark-3.5.0/spark-3.5.0-bin-hadoop3.tgz
tar xvf spark-3.5.0-bin-hadoop3.tgz
sudo mv spark-3.5.0-bin-hadoop3 /opt/spark
```

Open the ‘.bashrc’ file using a text editor:
```bash
vim ~/.bashrc
```

Add the following lines at the end of the file:

```bash
export SPARK_HOME=/opt/spark
export PATH=$PATH:$SPARK_HOME/bin
```

Save the file and exit the text editor. Then, reload the ‘.bashrc’ file:

```bash
source ~/.bashrc
```


### Install Python and PySpark

Make sure you have Python installed on your system. PySpark requires Python 3.6 or later. You can check your Python version by running:
```bash
python --version
```

or

```bash
python3 --version
```

To install PySpark, you can use pip:

```bash
pip install pyspark
```
Running PySpark

After installation, you can start a PySpark shell by simply running:
```bash
pyspark
```
This will open an interactive PySpark shell where you can start typing your PySpark code.
5. Test Your Setup

To test your setup, you can run a simple Spark job in the PySpark shell. For example:
```python
rdd = sc.parallelize([1, 2, 3, 4, 5])
rdd.map(lambda x: x*x).collect()
```
This code creates an RDD, maps a function over it, and collects the results back to your driver program.


# Recomended Setup: Docker
Below is a step-by-step guide to get a Docker container running Spark using the Bitnami Spark image, and accessing the Spark Master Web UI for interfacing with your Spark cluster.
### Prerequisites

Docker: Ensure Docker is installed on your system. You can download it from Docker's official website.

### Step 1: Pull the Bitnami Spark Docker Image

First, pull the Bitnami Spark image from Docker Hub:

```bash
docker pull bitnami/spark
```

This command downloads the latest Spark image provided by Bitnami.
### Step 2: Run the Spark Container

Run the Spark container with port 8080 (the default port for the Spark Master Web UI) mapped to a port on your host machine. This step allows you to access the Spark Web UI from your browser.

```bash
docker run -d -p 8080:8080 --name spark-master bitnami/spark
```

Here, -d runs the container in detached mode, -p 8080:8080 maps the container's port 8080 to port 8080 on your host, and --name spark-master names your container spark-master.
### Step 3: Access the Spark Master Web UI

Once the container is running, you can access the Spark Master Web UI through your web browser:

```arduino
http://localhost:8080
```

This URL directs you to the Spark Master Web UI, where you can see details about your Spark cluster.
### Step 4: Interacting with Spark

To run Spark jobs or interact with the Spark shell, you'll need to access the Spark container's command line. Use the following command:

```bash
docker exec -it spark-master /bin/bash
```
-or- 

```bash
docker run -d -p 8080:8080 -v /path/to/working/directory:/opt/spark-data --name spark-master bitnami/spark
```

This command opens a Bash shell inside the spark-master container. From here, you can start the Spark shell or submit Spark jobs.
### Step 5: Submitting Spark Jobs

To submit Spark jobs, use the spark-submit command inside the container. For example:

```bash

spark-submit --class org.apache.spark.examples.SparkPi \
    --master spark://spark-master:7077 \
    /opt/bitnami/spark/examples/jars/spark-examples_2.12-3.1.2.jar 1000
```

This command runs an example Spark job (calculating Pi) on your Spark cluster.  
### Step 6: Stopping the Container

When you're done, you can stop the Spark container using:

```bash
docker stop spark-master
```

And to start it again, use:

```bash
docker start spark-master
```

Additional Notes:

- Data Analysis: For data analysis tasks, you might want to mount a volume to your container to access datasets or scripts from your host machine. Use the -v option in the docker run command to mount a volume.
- Custom Configuration: If you need a custom Spark configuration, you can create a Dockerfile that builds from the Bitnami Spark image and adds your custom configuration files.
- Cluster Setup: This guide covers a single-node Spark setup. For a multi-node cluster, you'll need to set up additional worker containers and configure networking between them.

This guide should help you get started with running Spark in a Docker container using the Bitnami image and accessing the Spark Web UI for basic interactions. For more complex setups or specific data analysis tasks, you may need to adjust the configuration and setup accordingly.

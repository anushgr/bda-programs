Absolutely. Since you've already completed and pushed **`anushgr/bda-exp2:latest`**, your internal-exam workflow can be much shorter than the original setup.

The important thing to understand is that **pulling the image alone does not create the NameNode/DataNode cluster**. You still need the `docker-compose.yml` and `config` files to tell Docker how to start the two Hadoop services.

## Experiment 2 — Internal Exam Procedure

### Step 0 — Get your experiment files

On the lab PC, you need these files in one folder:

```text
exp-2/
├── docker-compose.yml
├── config
└── HdfsDemo.java
```

You can keep your existing files on a USB/Git repo/etc.

Your Docker image comes from:

```text
anushgr/bda-exp2:latest
```

---

# Part 1 — Pull your Docker image

Open **Git Bash / terminal** and run:

```bash
docker pull anushgr/bda-exp2:latest
```

Verify:

```bash
docker images | grep bda-exp2
```

You should see:

```text
anushgr/bda-exp2   latest
```

---

# Part 2 — Check your files

Go into your Experiment 2 directory:

```bash
cd path/to/exp-2
```

For example:

```bash
cd ~/Desktop/projects/clg/bda-lab-sem-7/bda-programs/exp-2
```

Check:

```bash
ls -l
```

You should have:

```text
config
docker-compose.yml
HdfsDemo.java
```

---

# Part 3 — Make sure Compose uses your Docker Hub image

Your `docker-compose.yml` should use:

```yaml
services:
  namenode:
    image: anushgr/bda-exp2:latest
    hostname: namenode
    command: ["hdfs", "namenode"]
    ports:
      - "9870:9870"
    env_file: ./config
    environment:
      ENSURE_NAMENODE_DIR: "/tmp/hadoop-root/dfs/name"

  datanode:
    image: anushgr/bda-exp2:latest
    command: ["hdfs", "datanode"]
    env_file: ./config
```

And `config` should contain:

```text
CORE-SITE.XML_fs.default.name=hdfs://namenode
CORE-SITE.XML_fs.defaultFS=hdfs://namenode
HDFS-SITE.XML_dfs.namenode.rpc-address=namenode:8020
HDFS-SITE.XML_dfs.replication=1
```

**This is important:** don't use `apache/hadoop:withjava` in the Compose file for your internal. Use your pushed image:

```text
anushgr/bda-exp2:latest
```

---

# Part 4 — Start Hadoop

Run:

```bash
docker compose up -d
```

Check:

```bash
docker compose ps
```

You want:

```text
namenode    Up
datanode    Up
```

You can also run:

```bash
docker ps
```

You should see the two containers.

---

# Part 5 — Enter the NameNode

Run:

```bash
docker compose exec namenode bash
```

You'll get something like:

```text
[root@namenode hadoop]#
```

Now check Hadoop:

```bash
hadoop version
```

And Java:

```bash
javac -version
```

Your image already contains Java 8/JDK, so `javac` should work.

---

# Part 6 — Create the HDFS directory

Run:

```bash
hdfs dfs -mkdir -p /user/student
```

Check:

```bash
hdfs dfs -ls /user
```

Expected:

```text
/user/student
```

You can also verify the DataNode:

```bash
hdfs dfsadmin -report
```

You should see:

```text
Live datanodes (1)
```

---

# Part 7 — Copy `HdfsDemo.java`

Exit the container:

```bash
exit
```

Now you're back on your host terminal.

Copy the Java file into the NameNode:

```bash
docker compose cp HdfsDemo.java namenode:/opt/hadoop/HdfsDemo.java
```

Enter the container again:

```bash
docker compose exec namenode bash
```

Then:

```bash
cd /opt/hadoop
```

Verify:

```bash
ls -l HdfsDemo.java
```

---

# Part 8 — Compile the Java program

Clean previous files:

```bash
rm -rf classes hdfsdemo.jar
```

Set Hadoop classpath:

```bash
export HADOOP_CLASSPATH=$(hadoop classpath)
```

Create the classes directory:

```bash
mkdir classes
```

Compile:

```bash
javac -classpath $HADOOP_CLASSPATH -d classes HdfsDemo.java
```

Check:

```bash
ls -l classes/
```

You should see:

```text
HdfsDemo.class
```

---

# Part 9 — Create the JAR

Run:

```bash
jar -cvf hdfsdemo.jar -C classes .
```

Then verify:

```bash
jar -tf hdfsdemo.jar
```

Expected:

```text
META-INF/
META-INF/MANIFEST.MF
HdfsDemo.class
```

---

# Part 10 — Execute the experiment

This is the main command:

```bash
hadoop jar hdfsdemo.jar HdfsDemo
```

You should get:

```text
Wrote file: /user/student/quangle.txt
From start : On the top of the Crumpetty Tree
After seek : The Quangle Wangle sat
Length     : 56 bytes
Replication: 1
--- listStatus /user/student ---
  quangle.txt
--- globStatus *.txt ---
  quangle.txt
```

At this point you've demonstrated the main experiment.

---

# Part 11 — Demonstrate the second file

The lab also has you create another HDFS file.

Run:

```bash
echo "hello hdfs" | hdfs dfs -put - /user/student/notes.txt
```

Then:

```bash
hadoop jar hdfsdemo.jar HdfsDemo
```

Now you should see:

```text
--- listStatus /user/student ---
  notes.txt
  quangle.txt
--- globStatus *.txt ---
  notes.txt
  quangle.txt
```

That completes the experiment.

---

# Part 12 — Optional NameNode UI

Because your Compose file maps:

```text
9870:9870
```

you can open:

```text
http://localhost:9870
```

in the browser.

This shows the Hadoop NameNode web interface.

---

# Part 13 — Cleanup

After you're done with the internal:

First exit the container:

```bash
exit
```

Then:

```bash
docker compose down
```

---

# 🚀 Your actual internal-exam cheat sheet

Once you've practiced this once, **these are basically the commands you need to remember**:

### Host

```bash
docker pull anushgr/bda-exp2:latest
```

```bash
cd path/to/exp-2
```

```bash
docker compose up -d
```

```bash
docker compose ps
```

```bash
docker compose cp HdfsDemo.java namenode:/opt/hadoop/HdfsDemo.java
```

```bash
docker compose exec namenode bash
```

### Inside NameNode

```bash
hdfs dfs -mkdir -p /user/student
```

```bash
cd /opt/hadoop
```

```bash
rm -rf classes hdfsdemo.jar
```

```bash
export HADOOP_CLASSPATH=$(hadoop classpath)
```

```bash
mkdir classes
```

```bash
javac -classpath $HADOOP_CLASSPATH -d classes HdfsDemo.java
```

```bash
jar -cvf hdfsdemo.jar -C classes .
```

```bash
hadoop jar hdfsdemo.jar HdfsDemo
```

### Second-file demonstration

```bash
echo "hello hdfs" | hdfs dfs -put - /user/student/notes.txt
```

```bash
hadoop jar hdfsdemo.jar HdfsDemo
```

### Finish

```bash
exit
docker compose down
```

## One important thing to prepare before your internal

**Test your Docker Hub image from a completely clean state once.**

That means delete your local `anushgr/bda-exp2` image, pull it again, and run the Compose setup using the pulled image. That confirms you haven't accidentally relied on something that exists only in your current local Docker environment.

If that clean pull works, you can essentially reproduce the entire experiment in the internal using **Docker Hub + your 3 files + the command sequence above**.

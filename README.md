# lacat


This is a simple utility that exports CEF records from a Logger archive file.
It prints them to stdout by design, allowing the user to redirect them to a
file or pipe them into something else (grep, awk, whatever) for further
manipulation.

It is written in python (targetting 2.7 and 3.5) using only the standard
python library.  If you want to use the kafka integration, you will need to
install the optional `kafka-python` dependency.

## License

See accompanying LICENSE file.

tl;dr - MIT

## Usage

```
$ ./lacat -h
Usage: lacat [options] path_to_dat path_to_meta
Extracts cef events from Logger Archive files to stdout

Why is it called lacat?
    Because "Logger_Archive_cat" was too long to type.

Options:
  -h, --help            show this help message and exit
  -j, --json            export as json instead of raw cef
  -f FILTER, --filter=FILTER
                        specify a key=val to filter records by. multiple -s
                        k=v allowed
  -d DIRECTORY, --directory=DIRECTORY
                        specify a directory full of dat and csv files
  -k KAFKA_TOPIC, --kafka-topic=KAFKA_TOPIC
                        send events to kafka topic
  --kafka-servers=KAFKA_SERVERS
                        connect to kafka server name (can be comma-separated
                        list)
```

The usage is hopefully quite straightforward and the implementation is quite
fast.  I'm still optimizing it a bit to squeeze a bit more performance so check
back here for revisions.

## Installation

Place the file `lacat` in your path and make the file executable:
`chmod +x lacat.`

If you want to use the kafka integration install the kafka library:

```
pip install kafka-python
```

## Examples

Export raw CEF and capture in the file outfile.cef

```
./lacat ArcSight_Data_1_0504403158265495556.dat ArcSight_Metadata_1_504403158265495556.csv  > outfile.cef
```

Export raw CEF and send to a Kafka cluster

```
./lacat ArcSight_Data_1_0504403158265495556.dat ArcSight_Metadata_1_504403158265495556.csv -k lacat-events --kafka-servers=kafka1:9092,kafka2:9092,kafka3:9092
```

Export all CEF records, one per line in JSON format, and capture in outfile.json

```
./lacat -j ArcSight_Data_1_0504403158265495556.dat ArcSight_Metadata_1_504403158265495556.csv  > outfile.json
```

Filter results by limiting output to destination IP 10.0.0.1

```
./lacat -f dst=10.0.0.1  ArcSight_Data_1_0504403158265495556.dat ArcSight_Metadata_1_504403158265495556.csv
```

Filter results by limiting output to destination IP 10.0.0.1 and UDP events
only.

```
./lacat -f dst=10.0.0.1 -s proto=UDP  ArcSight_Data_1_0504403158265495556.dat ArcSight_Metadata_1_504403158265495556.csv
```

Multiple -s options can be specified to create an AND condition.  You can
always specify -j to get each record output in JSON.


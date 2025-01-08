# Python-Kafka Reference

```python linenums="1"
# A Python script for consuming a Kafka topic and writing it to file
# Sources:
#     https://www.slingacademy.com/article/how-to-write-a-kafka-consumer-in-python/
#     https://stackoverflow.com/questions/44407780/how-to-decode-deserialize-avro-with-python-from-kafka

import io

import avro.schema
from avro.io import DatumReader, BinaryDecoder
from confluent_kafka import Consumer, KafkaError


def print_assignment(_, partitions):
    print('Assignment:', partitions)


def decode(bytes_msg):
    bytes_io = io.BytesIO(bytes_msg)
    bytes_io.seek(5)  # Confluent adds 5 extra bytes before the typical Avro-formatted data
    decoder = BinaryDecoder(bytes_io)
    return datum_reader.read(decoder)


conf = {
    'bootstrap.servers': 'hostname:port',  # todo: add server
    'security.protocol': 'protocol',  # todo: add security protocol
    'sasl.mechanism': 'mechanism',  # todo: add SASL mechanism
    'sasl.username': 'username',  # todo: add username
    'sasl.password': 'password',  # todo: add password
    'group.id': 'group-id',  # todo: add group ID
    'auto.offset.reset': 'earliest'
}

schema = avro.schema.parse(open('topic-schema.avsc').read())  # todo: add schema file
datum_reader = DatumReader(schema)

consumer = Consumer(conf)
consumer.subscribe(['topic-name'], on_assign=print_assignment)  # todo: add topic name

with open('output.csv', 'w') as csv_file:
    while True:
        msg = consumer.poll(1.0)

        if msg is None:
            continue
        if msg.error():
            # noinspection PyProtectedMember
            if msg.error().code() == KafkaError._PARTITION_EOF:
                continue
            else:
                print(msg.error())
                break

        # noinspection PyArgumentList
        msg_value = msg.value()
        msg_dict = decode(msg_value)
        msg_str = str(msg_dict)

        # format the string for import via Conduktor
        dq_str = msg_str.replace('\'', '""')
        formatted_str = '"{}";"{}"\n'.format(dq_str, dq_str)  # in this case, output key and value were identical
        csv_file.write(formatted_str)
        print(formatted_str)

consumer.close()
```

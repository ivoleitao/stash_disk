# stash_disk
A [Stash](https://pub.dartlang.org/packages/stash) Disk storage extension

[![Pub Package](https://img.shields.io/pub/v/stash_disk.svg?style=flat-square)](https://pub.dartlang.org/packages/stash_disk)
[![Build Status](https://github.com/ivoleitao/shadertoy_api/workflows/build/badge.svg)](https://github.com/ivoleitao/stash_disk/actions)
[![Coverage Status](https://codecov.io/gh/ivoleitao/stash_disk/graph/badge.svg)](https://codecov.io/gh/ivoleitao/stash_disk)
[![Package Documentation](https://img.shields.io/badge/doc-stash_disk-blue.svg)](https://www.dartdocs.org/documentation/stash_disk/latest)
[![GitHub License](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Introduction

This storage extension for [stash](https://pub.dartlang.org/packages/stash) provides a disk based storage that relies on a highly performing binary serialization of the cache items through the use of [msgpack](https://msgpack.org) serialization format. This storage backend is particularly optimized to support `Stash` features, like expiration and eviction, highly dependent on the update of control fields on the cache entries upon user operations. On this storage backend the update of those header fields does not cause the update of the whole cache entry as some of the other storage implementations like [stash_hive](https://pub.dartlang.org/packages/stash_hive) or [stash_sembast](https://pub.dartlang.org/packages/stash_sembast).

## Getting Started

Add this to your `pubspec.yaml` (or create it):

```dart
dependencies:
    stash_disk: ^1.0.0-dev.1
```

Run the following command to install dependencies:

```dart
pub install
```

Optionally use the following command to run the tests:

```dart
pub run test
```

Finally, to start developing import the library:

```dart
import 'package:stash_disk/stash_disk.dart';
```

## Usage

The example bellow creates a cache with a disk storage backend that supports a maximum of 10 `User` objects. In the rather simple example bellow the serialization and deserialization of the object is coded by hand but normally it relies on the usage of libraries like [json_serializable](https://pub.dev/packages/json_serializable). Please take a look at the documentation of [stash](https://pub.dartlang.org/packages/stash) to gather additional information and to explore the full range of capabilities of the `Stash`library

```dart
  import 'dart:io';
  import 'package:stash_disk/stash_disk.dart';

  class Task {
    final int id;
    final String title;
    final bool completed;

    Task({this.id, this.title, this.completed = false});

    /// Creates a [Task] from json map
    factory Task.fromJson(Map<String, dynamic> json) => Task(
        id: json['id'] as int,
        title: json['title'] as String,
        completed: json['completed'] as bool);

    /// Creates a json map from a [Task]
    Map<String, dynamic> toJson() =>
        <String, dynamic>{'id': id, 'title': title, 'completed': completed};

    @override
    String toString() {
      return 'Task ${id}: "${title}" is ${completed ? "completed" : "not completed"}';
    }
  }

  void main() async {
    // Temporary path
    final path = Directory.systemTemp.path;

    // Creates cache with a disk based storage backend with the capacity of 10 entries 
    final cache = newDiskCache(path,
        maxEntries: 10, fromEncodable: (json) => Task.fromJson(json));

    // Adds a task with key 'task1' to the cache
    await cache.put(
        'task', Task(id: 1, title: 'Run stash example', completed: true));
    // Retrieves the task from the cache
    final value = await cache.get('task1');

    print(value);
  }

```

## Contributing

It is developed by best effort, in the motto of "Scratch your own itch!", meaning APIs that are meaningful for the author use cases.

If you would like to contribute with other parts of the API, feel free to make a [Github pull request](https://github.com/ivoleitao/stash_disk/pulls) as I'm always looking for contributions for:
* Tests
* Documentation
* New APIs

## Features and Bugs

Please file feature requests and bugs at the [issue tracker][tracker].

[tracker]: http://github.com/ivoleitao/stash_disk/issues/new

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details
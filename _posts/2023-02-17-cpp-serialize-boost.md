---
comments_id: 2
---

## Post 1: The unreasonable simplicity of serializing objects in C++ using Boost

I am a C++ newbee so maybe what I say will read naive. But I found the Boost library in C++ to be extremely useful.

I was working on a small personal C++ CLI application where I would store some objects on disk to be read later.

I have a structure and a map I want to serialize and store on disk. One challenge I felt will be be difficult for a library will be to handle the vector of strings variable in the struct but Boost was able to handle it like a pro.

#### Declaring the struct to be serialized

```cpp

#include <boost/archive/text_iarchive.hpp>
#include <boost/archive/text_oarchive.hpp>
#include <boost/serialization/string.hpp>
#include <boost/serialization/vector.hpp>
#include <boost/serialization/map.hpp>

struct Entry {
    unsigned long key;
    std::string dummy1;
    std::string dummy2;
    std::vector<std::string> dummy3;
    std::time_t time_of_creation;

    template <typename Archive>
    void serialize(Archive& ar, const unsigned int version)
    {
      ar & key;
      ar & dummy1;
      ar & dummy2;
      ar & dummy3;
      ar & time_of_creation;
    }
```
Similarly declaring a struct to searlize a c++ map

```cpp

struct EntryCollection {
    std::map<std::string, Entry> entry_map;

    template <typename Archive>
    void serialize(Archive& ar, const unsigned int version)
    {
      ar & entry_map;
    }
  };

```
This is declared in the header file for the structs. Now, lets see how we can read and write objects.

#### Reading and Writing to/from file

#include <fstream>
#include "entry.h"
    
```cpp

void write_to_file(const std::map<std::string, Entry>
    &entry, const std::string &filename) {
  std::ofstream ofs(filename);
  boost::archive::text_oarchive oa(ofs);
  // write class instance to archive
  oa << entry;
}


std::map<std::string, Entry>
    read_from_file(const std::string &filename) {
  std::map<std::string, Entry> entry;
  std::ifstream ifs(DEFAULT_FILE_NAME);
  boost::archive::text_iarchive ia(ifs);
  // write class instance to archive
  ia >> entry;
  return entry;
}


```

Works like a charm. I am very interested in the internals of this library. Will dig deeper to understand it more. What do you think?

Take care and keep learning. Will see you soon.



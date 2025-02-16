# GMD File Format

## Introduction

The GMD file format is used to store level data for Geometry Dash. This document provides a detailed description of the GMD file format, including its structure, fields, and data types. Additionally, it includes instructions on how to read and write GMD files with examples.

## Structure

A GMD file consists of a header and a body. The header contains metadata about the file, while the body contains the actual level data.

### Header

The header includes the following fields:

- **Magic Number**: A unique identifier for the GMD file format.
- **Version**: The version of the GMD file format.
- **Level ID**: A unique identifier for the level.
- **Level Name**: The name of the level.
- **Creator**: The name of the level creator.
- **Description**: A description of the level.
- **Difficulty**: The difficulty rating of the level.
- **Length**: The length of the level.
- **Stars**: The number of stars awarded for completing the level.
- **Coins**: The number of coins in the level.
- **Objects**: The number of objects in the level.

### Body

The body contains the actual level data, which is a list of objects. Each object has the following fields:

- **Object ID**: A unique identifier for the object.
- **X Position**: The X coordinate of the object.
- **Y Position**: The Y coordinate of the object.
- **Rotation**: The rotation of the object.
- **Scale**: The scale of the object.
- **Color**: The color of the object.
- **Layer**: The layer of the object.
- **Group**: The group of the object.

## Data Types

The GMD file format uses the following data types:

- **Integer**: A 32-bit signed integer.
- **Float**: A 32-bit floating-point number.
- **String**: A UTF-8 encoded string.
- **Boolean**: A boolean value (true or false).

## Reading GMD Files

To read a GMD file, follow these steps:

1. Open the GMD file in binary mode.
2. Read the header fields.
3. Read the body fields.
4. Parse the level data.

### Example

```cpp
#include <iostream>
#include <fstream>
#include <string>

struct GMDHeader {
    int magicNumber;
    int version;
    int levelID;
    std::string levelName;
    std::string creator;
    std::string description;
    int difficulty;
    int length;
    int stars;
    int coins;
    int objects;
};

struct GMDObject {
    int objectID;
    float xPos;
    float yPos;
    float rotation;
    float scale;
    std::string color;
    int layer;
    int group;
};

void readGMDFile(const std::string& filePath) {
    std::ifstream file(filePath, std::ios::binary);
    if (!file.is_open()) {
        std::cerr << "Failed to open file: " << filePath << std::endl;
        return;
    }

    GMDHeader header;
    file.read(reinterpret_cast<char*>(&header.magicNumber), sizeof(header.magicNumber));
    file.read(reinterpret_cast<char*>(&header.version), sizeof(header.version));
    file.read(reinterpret_cast<char*>(&header.levelID), sizeof(header.levelID));

    std::getline(file, header.levelName, '\0');
    std::getline(file, header.creator, '\0');
    std::getline(file, header.description, '\0');

    file.read(reinterpret_cast<char*>(&header.difficulty), sizeof(header.difficulty));
    file.read(reinterpret_cast<char*>(&header.length), sizeof(header.length));
    file.read(reinterpret_cast<char*>(&header.stars), sizeof(header.stars));
    file.read(reinterpret_cast<char*>(&header.coins), sizeof(header.coins));
    file.read(reinterpret_cast<char*>(&header.objects), sizeof(header.objects));

    std::vector<GMDObject> objects(header.objects);
    for (auto& object : objects) {
        file.read(reinterpret_cast<char*>(&object.objectID), sizeof(object.objectID));
        file.read(reinterpret_cast<char*>(&object.xPos), sizeof(object.xPos));
        file.read(reinterpret_cast<char*>(&object.yPos), sizeof(object.yPos));
        file.read(reinterpret_cast<char*>(&object.rotation), sizeof(object.rotation));
        file.read(reinterpret_cast<char*>(&object.scale), sizeof(object.scale));

        std::getline(file, object.color, '\0');

        file.read(reinterpret_cast<char*>(&object.layer), sizeof(object.layer));
        file.read(reinterpret_cast<char*>(&object.group), sizeof(object.group));
    }

    file.close();

    // Process the level data
    // ...
}

int main() {
    readGMDFile("path/to/level.gmd");
    return 0;
}
```

## Writing GMD Files

To write a GMD file, follow these steps:

1. Open the GMD file in binary mode.
2. Write the header fields.
3. Write the body fields.
4. Serialize the level data.

### Example

```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <vector>

struct GMDHeader {
    int magicNumber;
    int version;
    int levelID;
    std::string levelName;
    std::string creator;
    std::string description;
    int difficulty;
    int length;
    int stars;
    int coins;
    int objects;
};

struct GMDObject {
    int objectID;
    float xPos;
    float yPos;
    float rotation;
    float scale;
    std::string color;
    int layer;
    int group;
};

void writeGMDFile(const std::string& filePath, const GMDHeader& header, const std::vector<GMDObject>& objects) {
    std::ofstream file(filePath, std::ios::binary);
    if (!file.is_open()) {
        std::cerr << "Failed to open file: " << filePath << std::endl;
        return;
    }

    file.write(reinterpret_cast<const char*>(&header.magicNumber), sizeof(header.magicNumber));
    file.write(reinterpret_cast<const char*>(&header.version), sizeof(header.version));
    file.write(reinterpret_cast<const char*>(&header.levelID), sizeof(header.levelID));

    file.write(header.levelName.c_str(), header.levelName.size() + 1);
    file.write(header.creator.c_str(), header.creator.size() + 1);
    file.write(header.description.c_str(), header.description.size() + 1);

    file.write(reinterpret_cast<const char*>(&header.difficulty), sizeof(header.difficulty));
    file.write(reinterpret_cast<const char*>(&header.length), sizeof(header.length));
    file.write(reinterpret_cast<const char*>(&header.stars), sizeof(header.stars));
    file.write(reinterpret_cast<const char*>(&header.coins), sizeof(header.coins));
    file.write(reinterpret_cast<const char*>(&header.objects), sizeof(header.objects));

    for (const auto& object : objects) {
        file.write(reinterpret_cast<const char*>(&object.objectID), sizeof(object.objectID));
        file.write(reinterpret_cast<const char*>(&object.xPos), sizeof(object.xPos));
        file.write(reinterpret_cast<const char*>(&object.yPos), sizeof(object.yPos));
        file.write(reinterpret_cast<const char*>(&object.rotation), sizeof(object.rotation));
        file.write(reinterpret_cast<const char*>(&object.scale), sizeof(object.scale));

        file.write(object.color.c_str(), object.color.size() + 1);

        file.write(reinterpret_cast<const char*>(&object.layer), sizeof(object.layer));
        file.write(reinterpret_cast<const char*>(&object.group), sizeof(object.group));
    }

    file.close();
}

int main() {
    GMDHeader header = {
        0x474D4420, // Magic Number
        1,          // Version
        12345,      // Level ID
        "Sample Level", // Level Name
        "Creator",      // Creator
        "This is a sample level.", // Description
        5,          // Difficulty
        120,        // Length
        10,         // Stars
        3,          // Coins
        2           // Objects
    };

    std::vector<GMDObject> objects = {
        { 1, 100.0f, 200.0f, 0.0f, 1.0f, "red", 1, 1 },
        { 2, 150.0f, 250.0f, 45.0f, 0.5f, "blue", 2, 2 }
    };

    writeGMDFile("path/to/level.gmd", header, objects);
    return 0;
}
```

## Conclusion

This document provides a detailed description of the GMD file format, including its structure, fields, and data types. It also includes instructions on how to read and write GMD files with examples. By following these instructions, you can successfully read and write GMD files for Geometry Dash levels.

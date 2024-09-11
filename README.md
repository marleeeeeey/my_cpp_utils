# MarleeeeeeY Common C++ Utilities

Here are some common C++ utilities that I frequently use in my projects. They include functions for working with:
- JSON configuration files,
- useful macros for logging and formatting,
- mathematical functions,
- string manipulation functions.

### App global logger based on spdlog

```cpp
std::filesystem::path logFilePath = "logs/wofares_game_engine.log";
spdlog::level::level_enum logLevel = spdlog::level::trace;
utils::Logger::Init(logFilePath, logLevel);
MY_LOG(trace, "Player {} changed weapon to {}", playerInfo.number, playerInfo.currentWeapon);
```

### Macros to format to std::string

```cpp
MY_FMT("{:.2f}/{:.2f} (Gr/Sc)", gravity, cameraScale);
```

### Configuration based on JSON file

```json
{
  "main": {
    "fps": 60,
    "webFps": 20,
    "logLevel": "info"
  },
  "GameOptions": {
    "windowOptions": {
      "cameraScale": 2
    }
  }
}
```

```cpp
std::filesystem::path configFilePath = "config.json";
utils::Config::InitInstanceFromFile(configFilePath);                                // <=== Setup the config file

const Uint32 frameDelayMs = 1000 / utils::GetConfig<unsigned, "main.webFps">();     // <=== Read one value from json

struct GameOptions
{
    // ...
    WindowOptions windowOptions;
    // ...
    NLOHMANN_DEFINE_TYPE_INTRUSIVE(GameOptions, windowOptions)
};
auto gameOptions = utils::GetConfig<GameOptions>("GameOptions");                    // <=== Read a struct from json

```

### String utilities

```cpp
std::string ToString(const T& value);                       // Convert any value to string
T FromString(std::string_view string);                      // Convert string to any value
T FromChar(char c);                                         // Convert char to any value
void TrimLeft(std::string& s);                              // Trim from start (in place)
void TrimRight(std::string& s);                             // Trim from end (in place)
void Trim(std::string& s);                                  // Trim from both ends (in place)
std::string TrimLeftCopy(std::string s);                    // Trim from start (copying)
std::string TrimRightCopy(std::string s);                   // Trim from end (copying)
std::string TrimCopy(std::string s);                        // Trim from both ends (copying)
std::string JoinStrings(                                    // Join strings with separator
    const std::vector<std::string>& strings,
    const std::string& separator);
std::string JoinStrings(                                    // Join container with separator
    const T& container,
    const std::string& separator = ", ");
```

### Math utilities

```cpp
bool IsEqual(T lhs, T rhs, T eps = 0.0001f);                // Compare two values with epsilon
bool RandomTrue(float probabilityOfTrue = 0.5f);            // Random true with probability
T Random(T min, T max);                                     // Random number in range
std::optional<size_t> RandomIndexOpt(const T& container);   // Random index from container
T Sign(T val);                                              // Sign of value
glm::vec2 GetRandomCoordinateAround(                        // Random point around center
    const glm::vec2& center, float radius);
T GetArea(const sf::Vector2<T>& size);                      // Area of rectangle
sf::Vector2<T> Normalize(const sf::Vector2<T>& v);          // Normalize vector
T RoundStep(T value, T step);                               // Round value to step
```

### How to build

```bash
# Branch 2024.06.15 is used because it has no errors with spdlog.
# vcpkg reads dependencies from vcpkg.json
git clone --branch 2024.06.15 --single-branch https://github.com/microsoft/vcpkg && .\\vcpkg\\bootstrap-vcpkg.bat && .\\vcpkg\\vcpkg install --triplet=x64-windows

# Configure cmake with vcpkg preset. Preset is defined in CMakePresets.json
cmake -S . -B build --preset use_vcpkg

# Build the project. -k 0 means stop on first error
cmake --build build -- -k 0
```

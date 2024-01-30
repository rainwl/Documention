# C++ Timer

## Code
```C++
// ReSharper disable CppClangTidyCppcoreguidelinesSpecialMemberFunctions
#pragma once
#include <chrono>
#include <string>

class Timer {
public:
  explicit Timer(std::string name);

  void start();

  void stop();

  ~Timer();

private:
  std::string name_;
  std::chrono::time_point<std::chrono::high_resolution_clock> start_time_;
  bool has_stopped_;
};

```

```C++
#include <iomanip>
#include "Timer.h"

Timer::Timer(std::string name): name_(std::move(name)), has_stopped_(false) {}

void Timer::start() {
  start_time_ = std::chrono::high_resolution_clock::now();
}

void Timer::stop() {
  if (!has_stopped_) {
    const auto end_time = std::chrono::high_resolution_clock::now();
    const std::chrono::duration<double, std::milli> duration = end_time - start_time_;
    // std::cout << name_ << " Duration: " << duration.count() << " ms" << '\n';

    // Milliseconds to two decimal places: x.xx ms
    // std::cout << std::fixed << std::setprecision(2) << name_ << " Duration: " << duration.count() << " ms" << '\n';

    // Milliseconds to four decimal places: x.xxxx ms
    std::cout << std::fixed << std::setprecision(4) << name_ << " Duration: " << duration.count() << " ms" << '\n';

    // microsecond: x.xx µs
    // const std::chrono::duration<double, std::micro> duration_micro = end_time - start_time_;
    // std::cout << std::fixed << std::setprecision(2) << name_ << " Duration: " << duration_micro.count() << " µs" << '\n';
    
    has_stopped_ = true;
  }
}

Timer::~Timer() {
  stop();// Ensure timing stops if timer object goes out of scope
}

```

## Usage
```C++
  Timer timer ("fusion data");
  timer.start();
  const auto fusion_data = std::make_shared<FusionData>("FusionData");
  scenes.push_back(fusion_data);
  timer.stop();

  Timer timer2 ("mesh collision");
  timer2.start();
  const auto mesh_collision = std::make_shared<MeshCollision>("MeshCollision");
  scenes.push_back(mesh_collision);
  timer2.stop();
```

`output`
```Bash
fusion data Duration: 0.0005 ms
mesh collision Duration: 0.0032 ms
```
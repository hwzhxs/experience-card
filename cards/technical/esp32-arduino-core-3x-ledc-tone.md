---
title: ESP32 Arduino core 3.x 下 ledcWriteTone 没声音，要显式设 duty
created: 2026-04-23
trigger: M5StickC Plus 烧完没蜂鸣器声音，M5.Beep.tone() / ledcWriteTone() 都调了
---

## Principle

ESP32 Arduino core 3.x 重写了 LEDC API。老代码（或基于老 API 封装的库，例如 M5StickCPlus 0.1.1 的 `Speaker.cpp`）里 `ledcWriteTone(pin, freq)` 单独调用时**不会发声** — 它只配置频率，duty cycle 保持默认 0（静音）。必须额外 `ledcWrite(pin, duty)` 设一个非 0 duty 才出声。

## Failure Mode

- 代码调了 `M5.Beep.tone(freq, ms)` 或 `ledcWriteTone(pin, freq)`
- 编译通过、上传成功，但完全没声音
- 换频率、换引脚、换音量都无效 — 因为 duty 一直是 0

## Fix

绕过库的 Beep 封装，手写 LEDC：

```cpp
#define BUZZER_PIN 2   // M5StickC Plus 蜂鸣器在 GPIO2

void setup() {
  ledcAttach(BUZZER_PIN, 4000, 8);  // pin, base freq, resolution bits
}

void buzz(int freq, int ms) {
  ledcWriteTone(BUZZER_PIN, freq);
  ledcWrite(BUZZER_PIN, 128);       // ← 关键：50% duty
  delay(ms);
  ledcWrite(BUZZER_PIN, 0);
  ledcWriteTone(BUZZER_PIN, 0);
}
```

非阻塞版本：设 `buzzEndAt = millis() + ms`，在 loop 里检查到期后调 `ledcWrite(pin, 0)`。

## Detection

症状明确：用了 LEDC tone API 但完全无声。先在 setup 里加一段开机测试音（三个递升 tone），有声 = LEDC 通路 OK，问题就在具体调用没设 duty。

## Related

- 适用范围：所有用 ESP32 core 3.x + 老 LEDC 代码的项目（M5 系列、ESP32 裸板、IoT 玩具）
- 对应 ESP32 Arduino core 升级：2.x → 3.x，`ledcSetup + ledcAttachPin` 两步 API 合并成 `ledcAttach` 一步
- M5StickCPlus 0.1.1 库 `src/utility/Speaker.cpp` 有这个 bug，可能新版会修，但不要等

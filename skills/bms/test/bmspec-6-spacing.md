# bmspec-6-spacing: Note 间距（#SPEED）

> 来源：[bmspec spacing/](https://github.com/bemusic/bmspec/tree/master/features/spacing)
> SPEED 命令影响 Note 滚动间距，支持多值线性插值。

---

## basic.feature —— SPEED 基础

```gherkin

Feature: Spacing (#SPEED)

  Scenario: Without Speed Set
    Given a BMS file as follows
      """
      #SPEED01 0.5
      """
    Then note spacing at beat 1 is 1
    And note spacing at beat 100 is 1

  Scenario: With Single Speed
    Given a BMS file as follows
      """
      #SPEED01 0.5
      #001SP:0001
      """
    Then note spacing at beat 1 is 0.5
    And note spacing at beat 100 is 0.5

  Scenario: With Multiple Speed
    Given a BMS file as follows
      """
      #SPEED01 0.5
      #SPEED02 1.5
      #SPEED03 1
      #001SP:0102
      #002SP:03
      """
    Then note spacing at beat 1 is 0.5
    And note spacing at beat 4 is 0.5
    And note spacing at beat 5 is 1
    And note spacing at beat 6 is 1.5
    And note spacing at beat 7 is 1.25
    And note spacing at beat 8 is 1
    And note spacing at beat 100 is 1
```

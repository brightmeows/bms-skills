# bmspec-3-positioning: 滚动定位（#SCROLL）

> 来源：[bmspec positioning/](https://github.com/bemusic/bmspec/tree/master/features/positioning)
> SCROLL 命令影响滚动速度和位置累积。

---

## basic.feature —— SCROLL 基础

```gherkin

Feature: Positioning (#SCROLL)

  Scenario: Basic Scenario
    Given a BMS file as follows
      """
      #SCROLL02 0.5
      #001SC:02
      """
    Then scrolling speed at beat 1 is 1
    And scrolling speed at beat 4 is 0.5
    And scrolling speed at beat 12345 is 0.5
    And scrolling position at beat 1 is 1
    And scrolling position at beat 4 is 4
    And scrolling position at beat 6 is 5

  Scenario: Initial Speed
    Given a BMS file as follows
      """
      #SCROLL02 0.5
      #000SC:02
      """
    Then scrolling speed at beat 0 is 0.5
    And scrolling speed at beat -1 is 0.5
    And scrolling speed at beat 12345 is 0.5
    And scrolling position at beat -1 is -0.5
```

## ext.feature —— SCROLL 扩展行

```gherkin

Feature: Positioning (#SCROLL) on Extension Lines

  Scenario: Basic Scenario
    Given a BMS file as follows
      """
      #SCROLL02 0.5
      #EXT #001SC:02
      """
    And scrolling speed at beat 4 is 0.5
```

# bmspec-4-random: 随机分支（#RANDOM）

> 来源：[bmspec random/](https://github.com/bemusic/bmspec/tree/master/features/random)
> RANDOM 控制流解析：条件分支与随机值序列。

---

## basic.feature —— RANDOM 基础

```gherkin

Feature: Basic RANDOM

  Scenario: Basic RANDOM (Scenario 1)
    Given the random number generator yields 1
    Given a BMS file as follows
      """
      #RANDOM 2
      #IF 1
      #00111:0100
      #ENDIF
      #IF 2
      #00111:0001
      #ENDIF
      """
    Then object 01 should be at beat 4

  Scenario: Basic RANDOM (Scenario 2)
    Given the random number generator yields 2
    Given a BMS file as follows
      """
      #RANDOM 2
      #IF 1
      #00111:0100
      #ENDIF
      #IF 2
      #00111:0001
      #ENDIF
      """
    Then object 01 should be at beat 6

  Scenario: Multiple RANDOM
    Given the random number generator yields 2, 1
    Given a BMS file as follows
      """
      #RANDOM 2
      #IF 1
      #00111:0100
      #ENDIF
      #IF 2
      #00111:0001
      #ENDIF
      #RANDOM 2
      #IF 1
      #00211:0200
      #ENDIF
      #IF 2
      #00211:0002
      #ENDIF
      """
    Then object 01 should be at beat 6
    And object 02 should be at beat 8
```

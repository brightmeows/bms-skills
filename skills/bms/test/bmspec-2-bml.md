# bmspec-2-bml: 长音（Long Note）

> 来源：[bmspec bml/](https://github.com/bemusic/bmspec/tree/master/features/bml)
> 两种长音模式：LNOBJ 与 LNTYPE1。

---

## LNOBJ.feature —— LNOBJ 模式

```gherkin
Feature: LNOBJ

  Scenario: Long Note Object
    Given a BMS file as follows
      """
      #LNOBJ XX
      #00111:01XX02XX03XX04XX
      """
    Then there should be 4 playable notes
     And object 01 should be a long note from beat 4 to 4.5
     And object 02 should be a long note from beat 5 to 5.5
     And object 03 should be a long note from beat 6 to 6.5
     And object 04 should be a long note from beat 7 to 7.5
```

## LNTYPE1.feature —— 独立长音通道

```gherkin
Feature: LNTYPE 1

  Scenario: Long Note Channel
    Given a BMS file as follows
      """
      #00151:0101020203030404
      """
    Then there should be 4 playable notes
     And object 01 should be a long note from beat 4 to 4.5
     And object 02 should be a long note from beat 5 to 5.5
     And object 03 should be a long note from beat 6 to 6.5
     And object 04 should be a long note from beat 7 to 7.5
```

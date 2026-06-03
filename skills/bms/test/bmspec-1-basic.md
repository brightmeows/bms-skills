# bmspec-1-basic: BMS 基础解析

> 来源：[bmspec basic/](https://github.com/bemusic/bmspec/tree/master/features/basic)
> BMS 格式基础解析：句子、头部命令、对象定位、拍号、BPM、STOP、WAV 引用。

---

## 01-Sentences.feature —— 句子解析

```gherkin
Feature: Basic Sentence Parsing

  This feature is about parsing BMS at its most basic level.

  Scenario: Parsing a BMS file with standard sentences
    Given a BMS file as follows
      """
      #TITLE BY MY SIDE
      #ARTIST flicknote
      #00101:0100010001
      This is a comment
      """
    Then there should be 2 header sentences
    And there should be 1 channel sentence

  Scenario: Parsing a malformed command line
    Given a BMS file as follows
      """
      #TITLE BY MY SIDE
      #ARTIST:flicknote
      """
    Then there should be 1 header sentence

```

## 02-Header.feature —— 头部命令解析

```gherkin
Feature: Header Sentence Parsing

  Scenario: Standard header
    Given a BMS file as follows
      """
      #TITLE BY MY SIDE
      #ARTIST flicknote
      """
    Then the header "TITLE" should have value "BY MY SIDE"
    And the header "ARTIST" should have value "flicknote"

  Scenario: Header with multiple spaces as delimiter
    Given a BMS file as follows
      """
      #TITLE      BY MY SIDE
      """
    Then the header "TITLE" should have value "BY MY SIDE"

  Scenario: Indented header
    Given a BMS file as follows
      """
      #TITLE BY MY SIDE
        #ARTIST flicknote
      """
    Then the header "TITLE" should have value "BY MY SIDE"
    And the header "ARTIST" should have value "flicknote"

  Scenario: Case-insensitive header
    Given a BMS file as follows
      """
      #Title BY MY SIDE
      #Artist flicknote
      """
    Then the header "TITLE" should have value "BY MY SIDE"
    And the header "ARTIST" should have value "flicknote"

  Scenario: Duplicated header lines
    Given a BMS file as follows
      """
      #TiTlE BEAT MUSIC SEQUENCE
      #tItLe BY*MY*SIDE
      """
    Then the header "TITLE" should have value "BY*MY*SIDE"

```

## 03-Objects.feature —— 对象解析

```gherkin

Feature: Basic Object Parsing

  Objects are central to the BMS file format.

  - They make up the song's notecharts, keysounds, and autoplay sounds.
  - They specify the song's BPM changes. 
  - They specify the song's background animation changes.
  - etc.

  Each object is placed on a timeline (measure+fraction) inside a channel.
  The channel determines the object's role. For example:

  - Objects in channel 01 are autoplay sounds.
  - Objects in channel 11 are notes for button 1.
  - Objects in channel 03 are for changing BPM.

  Scenario: Reading basic note objects
    Given a BMS file as follows
      """
      #00111:01000002
      #00311:0003
      """
    Then there should be 3 objects
    And object 01 should be on channel 11 at beat 4
    And object 02 should be on channel 11 at beat 7
    And object 03 should be on channel 11 at beat 14

  Scenario: Reading overlapped note objects
    Given a BMS file as follows
      """
      #00113:11111111
      #00113:0022332255224400
      #00113:0066
      """
    Then there should be 7 objects
    And object 66 should be on channel 13 at beat 6

  Scenario: Reading overlapped autoplay objects
    Given a BMS file as follows
      """
      #00101:11111111
      #00101:0022332255224400
      #00101:0066
      """
    Then there should be 11 objects

  Scenario: Reading empty channel sentences
    Given a BMS file as follows
      """
      #00111:
      """
    Then there should be 0 objects

```

## 04-Time-Signature.feature —— 拍号

```gherkin
Feature: Time Signature

  Scenario: Time signature
    Given a BMS file as follows
      """
      #00102:0.750
      #00111:0104
      #00211:02
      #00311:03
      """
    Then object 01 should be at beat 4
     And object 02 should be at beat 7
     And object 03 should be at beat 11
     And object 04 should be at beat 5.5
```

## 05-BPM.feature —— BPM 变化

```gherkin
Feature: BPM Changes

  Some songs change the speed during the song. This is called BPM changes.

  Scenario: BPM Change
    Given a BMS file as follows
      """
      #BPM 60
      #00003:0078
      #00111:01
      """
    Then object 01 should be at 3 seconds

  Scenario: Multiple BPM Changes
    Given a BMS file as follows
      """
      #BPM 100
      #00003:0060C0
      #00011:00010203
      #00111:04
      """
    Then object 01 should be at 0.6 seconds
    Then object 02 should be at 1.216667 seconds
    Then object 03 should be at 1.7375 seconds
    Then object 04 should be at 2.05 seconds

  Scenario: Extended BPM Change
    Given a BMS file as follows
      """
      #BPM 60
      #BPM01 120
      #00008:0001
      #00111:05
      """
    Then object 05 should be at 3 seconds
```

## 06-STOP.feature —— 停止

```gherkin
Feature: STOP

  For more gimmick in game, the notechart can be temporarily paused, using
  the `#STOP` command.

  Scenario: Basic Stop
    Given a BMS file as follows
      """
      #BPM 60
      #STOP11 96
      #00111:01000200
      #00109:00110000
      """
    Then object 01 should be at 4 seconds
     And object 02 should be at 8 seconds

  Scenario: Stop on Non 4/4 Time Signature
    Given a BMS file as follows
      """
      #BPM 60
      #STOP11 96
      #00102:0.75
      #00111:010002
      #00109:001100
      """
    Then object 01 should be at 4 seconds
     And object 02 should be at 8 seconds

  Scenario: Stop on Same Beat as BPM (Stop appears first)
    Given a BMS file as follows
      """
      #BPM 60
      #BPM11 30
      #STOP11 96
      #00111:01000200
      #00109:00110000
      #00108:00110000
      """
    Then object 01 should be at 4 seconds
     And object 02 should be at 11 seconds

  Scenario: Stop on Same Beat as BPM (BPM appears first)
    Given a BMS file as follows
      """
      #BPM 60
      #BPM11 30
      #STOP11 96
      #00111:01000200
      #00108:00110000
      #00109:00110000
      """
    Then object 01 should be at 4 seconds
     And object 02 should be at 11 seconds

  Scenario: Stop on Same Beat as Another Object
    Given a BMS file as follows
      """
      #BPM 120
      #STOP11 192
      #00111:01020304
      #00109:11000000
      """
    Then object 01 should be at 2 seconds
     And object 02 should be at 4.5 seconds
     And object 03 should be at 5 seconds
     And object 04 should be at 5.5 seconds
```

## 07-Basic-Info.feature —— 基本信息

```gherkin
Feature: Basic Info

  This feature describes basic song information parsing in the normal case.

  Scenario: Parsing basic song information
    Given a BMS file as follows
      """
      #TITLE BY MY SIDE
      #ARTIST flicknote
      #GENRE Trance Core
      #DIFFICULTY 2
      #PLAYLEVEL 5
      """
    Then song title should be "BY MY SIDE"
    And song artist should be "flicknote"
    And song genre should be "Trance Core"
    And song should have difficulty 2
    And song should have play level 5
```

## 08-WAV.feature —— WAV 定义

```gherkin
Feature: WAV Sentence Parsing

  Scenario: Reading WAV sentences
    Given a BMS file as follows
      """
      #TITLE #WAV test case
      #WAV01 index.mp3
      #WAVZZ wow.mp3
      #WAVAA zz.ogg
      """
    Then sound ZZ references file "wow.mp3"
    And sound 01 references file "index.mp3"
    And sound AA references file "zz.ogg"
    And sound 02 is a null reference
```

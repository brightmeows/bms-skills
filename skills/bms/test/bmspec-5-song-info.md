# bmspec-5-song-info: 歌曲信息扩展

> 来源：[bmspec song-info/](https://github.com/bemusic/bmspec/tree/master/features/song-info)
> 副标题与子艺术家信息的多种形式。

---

## Subtitle.feature —— 基本副标题

```gherkin
Feature: Subtitles (Basic)

  Some songs may have subtitles, such as [Beginner].

  http://hitkey.nekokan.dyndns.info/cmds.htm#SUBTITLE

  Scenario: Normal subtitle
    Given a BMS file as follows
      """
      #TITLE BY MY SIDE
      #SUBTITLE [TUTORIAL]
      """
    Then song title should be "BY MY SIDE"
    And song subtitle should be "[TUTORIAL]"
```

## Subtitle-Implicit.feature —— 隐式副标题

```gherkin
Feature: Subtitles (Implicit)

  Subtitles can be derived from `#TITLE`.

  http://hitkey.nekokan.dyndns.info/cmds.htm#TITLE-IMPLICIT-SUBTITLE

  Scenario Outline: Implicit subtitle
    Given a BMS file as follows
      """
      #TITLE <Value>
      """
    Then song title should be "<Title>"
    And song subtitle should be "<Subtitle>"
    Examples:
      | Value                      | Title      | Subtitle |
      | BY MY SIDE-Tutorial-       | BY MY SIDE | Tutorial |
      | BY MY SIDE～Tutorial～     | BY MY SIDE | Tutorial |
      | BY MY SIDE(Tutorial)       | BY MY SIDE | Tutorial |
      | BY MY SIDE[Tutorial]       | BY MY SIDE | Tutorial |
      | BY MY SIDE<Tutorial>       | BY MY SIDE | Tutorial |
```

## Subtitle-Multi.feature —— 多行副标题

```gherkin
Feature: Subtitles (Multiplex)

  Some songs may have multiple subtitles.

  http://hitkey.nekokan.dyndns.info/cmds.htm#SUBTITLES

  Scenario: Multiple subtitles
    Given a BMS file as follows
      """
      #TITLE BY MY SIDE
      #SUBTITLE (Keyboard)
      #SUBTITLE [TUTORIAL]
      """
    Then song title should be "BY MY SIDE"
    And song subtitle should be:
      """
      (Keyboard)
      [TUTORIAL]
      """
```

## Subartist.feature —— 子艺术家

```gherkin
Feature: Subartist

  Some songs may have sub-artists.

  http://hitkey.nekokan.dyndns.info/cmds.htm#SUBARTIST

  Scenario: Subartists
    Given a BMS file as follows
      """
      #SUBARTIST MOVIE: m
      #SUBARTIST NOTER: n
      #SUBARTIST TESTR: t
      """
    Then song subartist should be:
      """
      MOVIE: m
      NOTER: n
      TESTR: t
      """
```

+++
author = "Hugo Authors"
title = "MIDI再生 ピアノロールアプリ「midiano」「Synthesia」まとめ"
date = "2026-09-23"
description = ""
tags = [
    "piano",
]
categories = [
    "Music",
]
image = "https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/asusn-ctf-2/cover.png"
+++

MIDI再生 ピアノロールアプリ「midiano」「Synthesia」まとめ
<!--more-->

## Synthesia
https://synthesiagame.com/download

- 過去バージョン「Synthesia 9」なら無料で再生可能（HPからダウンロード可能）


##  Midiano
https://www.midiano.com

- Synthesia 風鍵盤アニメーション
- MIDIをドラッグ＆ドロップで即再生
- WebGLで高速
- 完全無料 & ログイン不要

### 設定例
- General
  - General:
    - Show Miliseconds: ON // ミリ秒が表示
    - Scroll sensitivity: 0.55 // スクロールの感度
  - Color Coding by Finger:
    - Enable color coding by finger: ON 
    - Color piano keys: ON
      - Default: #36A1FF // 右手のノートの色
      - Default: #A3D4FF // 左手のノートの色
- Video
  - General:
    - Seconds shown on screen: 4
  - Note Appearance:
    - Active Notes effect: OFF // 横に広がるエフェクトは不要
    - Enable 'lighter' draw: ON // ノートが光る
      - Shadow color: rgba(255, 255, 255, 0.31) // 影の色
      - Shadow blur: 50 
    - Stroke active notes: ON 
      - Stroke color: rgba(0, 0, 0, 0.5)
  - Particles:
    - Enable particles: ON
    - Particle Presets:
      - Sparks & Bubbles: ON



### Midiano Settings - 23092026-2125.txt
```
{
  "settings": {
    "enableScoreMode": false,
    "renderOffset": 0,
    "disableHotkeys": false,
    "startWithLastSong": false,
    "saveMidiDevices": false,
    "fitZoomOnNewSong": false,
    "saveIndexedDb": false,
    "playNotesOnScrollEnabled": false,
    "reverseNoteDirection": false,
    "showBPM": false,
    "showFps": false,
    "showMiliseconds": true,
    "showNoteDebugInfo": false,
    "showMarkersSong": false,
    "showMarkersTimeline": false,
    "inputNoteColorWhite": "rgba(40,155,155,0.8)",
    "inputNoteColorBlack": "rgba(40,155,155,0.8)",
    "inputInstrument": "acoustic_grand_piano",
    "scrollSensitivity": 0.55,
    "transposeOffset": 0,
    "anacrusisAmount": 0,
    "pitchNotationSystem": "Letter notation",
    "noteLabel": "None",
    "noteLabelColor": "rgba(255,255,255,1)",
    "showKeyNamesOnPianoWhite": false,
    "octaveLabel": "None",
    "showKeyNamesOnPianoBlack": false,
    "showPianoKeyNameFlats": false,
    "showKeyBindingsOnPiano": false,
    "enableColorCodingNote": false,
    "colorCodingNotePreset": "Dark Studio",
    "enableColorCodingNotePiano": false,
    "colorCodeNote0": "#B04A4A",
    "colorCodeNote1": "#B04A4A",
    "colorCodeNote2": "#B0724A",
    "colorCodeNote3": "#B0724A",
    "colorCodeNote4": "#B0A14A",
    "colorCodeNote5": "#6B9B6B",
    "colorCodeNote6": "#6B9B6B",
    "colorCodeNote7": "#4A9B8B",
    "colorCodeNote8": "#4A9B8B",
    "colorCodeNote9": "#4A6B9B",
    "colorCodeNote10": "#4A6B9B",
    "colorCodeNote11": "#6B4A9B",
    "enableColorCodingFinger": true,
    "colorCodingFingerPreset": "Dark Studio",
    "enableColorCodingFingerPiano": true,
    "colorCodeFinger1Right": "#7F7540",
    "colorCodeFinger2Right": "#3F6964",
    "colorCodeFinger3Right": "#7F4940",
    "colorCodeFinger4Right": "#3F4E7F",
    "colorCodeFinger5Right": "#5B3F7F",
    "colorCodeFingerDefaultRight": "rgba(53,161,255,1)",
    "colorCodeFinger1Left": "#BFAF5E",
    "colorCodeFinger2Left": "#6BA8A2",
    "colorCodeFinger3Left": "#B86B5E",
    "colorCodeFinger4Left": "#5E7BB8",
    "colorCodeFinger5Left": "#8B6BB8",
    "colorCodeFingerDefaultLeft": "rgba(163,212,255,1)",
    "enableAutoFingering": false,
    "fingeringButtons": null,
    "lyricsEnabled": false,
    "lyricsBackground": "rgba(255,255,255,0.9)",
    "lyricsFontColor": "rgba(0,0,0,1)",
    "lyricsFontColorPlayed": "rgba(100,100,100,1)",
    "lyricsFontColorActive": "rgba(0,150,0,1)",
    "lyricsFontSize": 40,
    "lyricsY": 30,
    "lyricsCursorEnabled": true,
    "lyricsBouncingCursor": true,
    "enableLoop": false,
    "autoLoop": true,
    "loopDelay": 4,
    "enableLoopBeats": true,
    "loopStart": 0,
    "loopEnd": 2,
    "muteLoop": true,
    "forceDelay": 0,
    "minNoteDuration": 0,
    "restrictInputRange": false,
    "minInputNote": 0,
    "maxInputNote": 88,
    "playalongSilentNotes": false,
    "playalongSilentNotesChannel": 3,
    "enableSheet": false,
    "enableFullSheet": false,
    "sheetMeasureScroll": false,
    "sheetAutoClef": false,
    "sheetFormatToStave": false,
    "overrideTimeSignature": "Auto",
    "sheetMinNoteDenom": "32nd",
    "hideRestsBelow": "16th",
    "sheetMinStaveGap": 5,
    "sheetMinMeasureWidth": 250,
    "sheetMinTickableWidth": 28,
    "enableGrandStaff": true,
    "sheetRenderInputNotes": true,
    "sheetShowHoverInfo": true,
    "renderSheetCursor": false,
    "sheetColorActiveNotes": true,
    "sheetHighlightActiveNotes": false,
    "sheetColorByTrack": true,
    "sheetActiveGrowEffect": false,
    "sheetActiveNotesColor": "rgba(255, 143, 0, 1)",
    "sheetActiveHighlightColor": "rgba(0, 255, 0, 0.12)",
    "sheetBgColor": "#dfdfdf",
    "clickablePiano": true,
    "highlightActivePianoKeys": true,
    "drawPianoKeyHitEffect": true,
    "pianoEnableLighter": false,
    "pianoWhiteKeyColor": "rgba(210,210,210, 1)",
    "pianoBlackKeyColor": "rgba(25,25,25,1)",
    "pianoBackgroundColor": "rgba(255,255,255,1)",
    "pianoVelvetColor": "rgba(125, 5, 3, 1)",
    "pianoShadowColor": "rgba(255, 255, 255, 0.31)",
    "pianoShadowBlur": 0,
    "pianoPosition": 0,
    "whiteKeyHeight": 100,
    "blackKeyHeight": 100,
    "enableKeyFlare": false,
    "keyFlareOnlyPiano": false,
    "keyFlareSize": 1,
    "keyFlareOpacity": 1,
    "keyFlareColor": "rgba(255,255,255,1)",
    "keyFlareOffset": 0,
    "exportPdfBackground": false,
    "pdfExportButtonGroup": null,
    "noteToHeightConst": 4,
    "drawMeasureLines": true,
    "measureLineThickness": 0.5,
    "measureLineColor": "rgba(255,255,255,0.3)",
    "enableDrawMeasureNumbers": true,
    "measureNumbersColor": "rgba(255,255,255,0.3)",
    "enableDrawBeatLines": true,
    "beatLineThickness": 0.4,
    "beatLineColor": "rgba(255,255,255,0.2)",
    "showHitKeys": false,
    "noteEnableLighterDraw": true,
    "noteShadowColor": "rgba(255,255,255,0.31)",
    "noteShadowBlur": 50,
    "strokeActiveNotes": true,
    "strokeActiveNotesColor": "rgba(0,0,0,0.5)",
    "strokeActiveNotesWidth": 2,
    "strokeNotes": false,
    "strokeNotesColor": "rgba(0,0,0,1)",
    "strokeNotesWidth": 1,
    "roundedNotes": true,
    "noteBorderRadius": 15,
    "noteWidth": 100,
    "minNoteHeight": 10,
    "noteEndedShrink": 1,
    "playedNoteFalloffSpeed": 1,
    "showSustainOnOffs": false,
    "showSustainChannels": false,
    "showSustainPeriods": false,
    "showSustainedNotes": false,
    "sustainedNotesOpacity": 50,
    "showParticles": true,
    "particlesMaxTextureSize": 500,
    "particlePreset": "Sparks & Bubbles_1",
    "particlePresetType": "Particles_Swirl_Right",
    "__0.40073858617148495": null,
    "particlesTexture": "star_06",
    "particlesBlending": "Additive",
    "particleOverrideColor": false,
    "particleColor": "rgba(255,0,0,1)",
    "particlesOpacity": 1,
    "particleSize": 14,
    "particleAmount": 25,
    "particleLife": 94,
    "particleSpeed": 1,
    "particleFadeOut": 1,
    "particlesShrink": 1,
    "particleSpeedX": 3.7,
    "particleSpeedY": 7.1,
    "particlesFriction": 0.418,
    "particleRising": 1.5,
    "particlesRotation": 0,
    "particlesRotationRandom": 180,
    "particlesRotationSpeed": 0.465,
    "particleNoiseResX": 0.001,
    "particleNoiseStrengthX": 1,
    "particleNoiseResY": 0.001,
    "particleNoiseStrengthY": 1,
    "particleNoiseStrengthSize": 0.5,
    "turbulenceXAmplitude": 40,
    "turbulenceXFrequency": 0.7,
    "turbulenceYAmplitude": 100,
    "turbulenceYFrequency": 0.4,
    "particleYOffset": 0,
    "particleDistributionX": "Across note",
    "particleDistributionXSpread": 100,
    "particleDistributionY": "Across note",
    "particleDistributionZ": "Behind notes",
    "pianoLineEnabled": false,
    "pianoLineSpeed": 6,
    "pianoLineResolution": 5,
    "pianoLineAmplitude": 3,
    "pianoLineNoiseA": 12.12,
    "pianoLineNoiseB": 0.01,
    "pianoLineNoiseC": 1.52,
    "pianoLineNoiseD": 5.28,
    "pianoLineColor": "rgba(0,128,255,1)",
    "pianoLineOpacity": 1,
    "pianoLineYOffset": 0,
    "extendBgPastPiano": true,
    "bgCol1": "rgba(40,40,40,0.8)",
    "bgCol2": "rgba(0,0,0,1)",
    "bgCol3": "rgba(0,0,0,1)",
    "bgCol4": "rgba(10,10,10,0.5)",
    "masterGain": 1,
    "inputGain": 1,
    "velocityStrength": 1,
    "soundfontName": "MusyngKite",
    "useHQPianoSoundfont": false,
    "sustainEnabled": true,
    "enableReverb": true,
    "reverbImpulseResponse": "SteinmanHall",
    "detune": 0,
    "enableMetronome": false,
    "metronomeMidiOutput": "Both",
    "metronomeNoteNumber": 76,
    "metronomeInstrument": "woodblock",
    "metronomeVolume": 0.1,
    "synchedMetronome": true,
    "bgTrackEnable": false,
    "bgTrackVolume": 50,
    "bgTrackSpeed": 100,
    "bgTrackOffset": 0,
    "bgTrackLoop": false,
    "bgTrack": "Default",
    "enableEqualizer": false,
    "equalizerGain0": 0,
    "equalizerGain1": 0,
    "equalizerGain2": 0,
    "equalizerGain3": 0,
    "equalizerGain4": 0,
    "adsrAttack": 0,
    "adsrDecay": 0,
    "adsrSustain": 100,
    "adsrReleaseKey": 0.35,
    "adsrReleasePedal": 0.35,
    "autoCacheMain": false,
    "autoCacheSongs": false,
    "autoCacheHqPiano": false,
    "autoCacheFluidR3": false,
    "autoCacheFatBoy": false,
    "autoCacheMusyngKite": false,
    "ppNoteMin": 39,
    "ppNoteMax": 51,
    "ppNoteAmount": 4,
    "playMode": "player",
    "lastSong": "花の塔.mid",
    "particlePresetName": "Sparks & Bubbles",
    "particleTypeName": "Particles_Swirl_Right",
    "keyBindings": {
      "0": [
        36
      ],
      "1": [
        27
      ],
      "2": [
        28
      ],
      "3": [
        29
      ],
      "4": [
        30
      ],
      "5": [
        31
      ],
      "6": [
        32
      ],
      "7": [
        33
      ],
      "8": [
        34
      ],
      "9": [
        35
      ],
      "Q": [
        37
      ],
      "W": [
        38
      ],
      "E": [
        39
      ],
      "R": [
        40
      ],
      "T": [
        41
      ],
      "Y": [
        42
      ],
      "U": [
        43
      ],
      "I": [
        44
      ],
      "O": [
        45
      ],
      "P": [
        46
      ],
      "A": [
        47
      ],
      "S": [
        48
      ],
      "D": [
        49
      ],
      "F": [
        50
      ],
      "G": [
        51
      ],
      "H": [
        52
      ],
      "J": [
        53
      ],
      "K": [
        54
      ],
      "L": [
        55
      ],
      "X": [
        57
      ],
      "Z": [
        56
      ],
      "C": [
        58
      ],
      "V": [
        59
      ],
      "B": [
        60
      ],
      "N": [
        61
      ],
      "M": [
        62
      ]
    },
    "hotKeyBindings": null
  }
}
```


## 参考

- 音が上からおちる動画の作り方～ピアノロールアプリ：Synthesia
https://note.com/supervisualizer/n/nbfb6eeb27bb7

- 完全無料で使える Web ピアノロール 3 選（MIDI対応・編集可・鍵盤発光）
https://qiita.com/qwer123123/items/202130e7b51378ee4b8d

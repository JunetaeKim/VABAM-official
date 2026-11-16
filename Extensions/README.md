# Extensions

Projects that apply or extend VABAM. Each one imports the official implementation in this repository without modifying it.

| Project | Signal | Summary |
|---|---|---|
| [VABAM-Circadian](https://github.com/JunetaeKim/VABAM-Circadian) | Circadian *Bmal1*/*Per2*-luciferase reporter recordings of cancer cell lines (10-min sampling, ~24-h period) | Transfers VABAM from beat-to-beat pulsatile signals (ABP, ECG) to circadian rhythms by re-deriving the filter cut-off range, so that θ controls circadian amplitude while z keeps the waveform. Adds circadian preprocessing, multi-source training with external test sets, and analyses linking the representation to clock-gene knockouts, circadian phenotypes and drug response. |

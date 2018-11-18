# rtl_power-fm-multipath

* FM broadcasting multipath distortion estimation by D/U ratio measurement
* Measurement with [rtl_power](http://kmkeen.com/rtl-power/)
* Python script copied from [rtl-sdr-misc](https://github.com/keenerd/rtl-sdr-misc)

## D/U ratio measurement method

* Proposed by [Komiya87] and [JushinFM]
* D/U ratio: Desired/Undesired ratio. On FM broadcasting: ratio of the strength of direct/reflected radio waves

### Methodology summary

(As shown in [Komiya87] and [JushinFM])

* Measure the maximum peak strength for +-50kHz spectrum of the target FM station
* Obtain the maximum value (Lmax) and minimum value (Lmin) within the spectrum
* Obtain the ratio of the maximum and minimum values L = Lmax / Lmin (note: Lmax and Lmin are real values (*not* in dB), and L must be > 1)
* The estimated D/U ratio R = (L+1) / (L-1) (in the real value, *not* in dB)

### Limitations 

* The result depends on the broadcast contents
* The estimation in [Komiya87] shows the range of D/U ratio R between 10~40 dB

### L and R examples in dB (where 20dB = x10)

(As shown in [JushinFM])

| L | R |
|---|---|
|1.8dB|19.7dB|
|5.2dB|10.7dB|
|10.2dB|5.5dB|

## Detailed measurement using RTL-SDR and rtl\_power

* Measure the spectrum peak values for 5 minutes in every 1 second
* Pick up the maximum values for the 5-minute measured spectrum for each frequency (peakhold.py)
* Note: the first measurement result is inaccurate so discarded

```sh
rtl_power -f 88.05M:88.15M:1k -i 1 -e 5m -g 8.7 -w hamming -F 9 -P | tail -n +2 > nhkfm3.csv
python peakhold.py nhkfm3.csv > nhkfm3-ph.csv
```

### Actual command output of rtl\_power

```
% rtl_power -f 85.05M:85.15M:1k -i 1 -e 5m -g 8.7 -w hamming -F 9 -P | tail -n +2 > fmosaka-3.csv
Number of frequency hops: 1
Dongle bandwidth: 1600000Hz
Downsampling by: 16x
Cropping by: 0.00%
Total FFT bins: 128
Logged FFT bins: 128
FFT bin size: 781.25Hz
Buffer size: 16384 bytes (5.12ms)
Reporting every 1 seconds
Found 1 device(s):
  0:  Realtek, RTL2838UHIDIR, SN: 00000001

Using device 0: Generic RTL2832U OEM
Found Rafael Micro R820T tuner
Tuner gain set to 8.70 dB.
[R82XX] PLL not locked!
```

## Example values of estimated D/U ratio

* NHK-FM Osaka 88.1MHz / nhkfm3.csv: L = 3.95dB, R = 13.01dB
* FM COCOLO 76.5MHz / cocolo3.csv: L = 3.51dB, R = 14.01dB
* FM802 80.2MHz / fm802-3.csv: L = 3.5dB, R = 14.03dB
* FM Osaka 85.1MHz / fmosaka3.csv: L = 3.11dB, R = 15.03dB

## References

* [JushinFM] Jushin Service Kabushiki Gaisha (受信サービス株式会社), "FM Broadcasting" 「FM放送」, <http://www.jushin-s.co.jp/jushin/fm.html#fm02>
* [Komiya87] Noriaki Komiya, "Multipath Measurement of FM Radio Waves by Means of Spectrum Analyzer", ITEJ Technical Report Vol.11, No.22, pp. 13~18, RE '87-29 (October 1987), Japanese paper copy: [スペクトル・アナライザによるFM放送波のマルチパス測定](https://www.jstage.jst.go.jp/article/tvtr/11/22/11_KJ00001967031/_article/-char/ja/)

## License

* [CC-BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)
* Reference papers in Japanese under `papers/` are obtained from [J-STAGE](https://www.jstage.jst.go.jp/)


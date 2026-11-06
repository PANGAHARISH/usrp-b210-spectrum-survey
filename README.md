# Cellular and Wi-Fi Spectrum Survey with USRP B210 (GNU Radio)

A simple receive-only spectrum survey. The USRP B210 captures 20 MHz of spectrum at a time, and a GNU Radio waterfall shows what is transmitting. No demodulation is done. The `freq` slider moves the view across 700 MHz to 6 GHz, covering LTE, 5G and Wi-Fi bands.

Signal chain:

    USRP B210 (RX2) -> QT GUI Sink (spectrum + waterfall)

## Hardware

| Item | Details |
|---|---|
| SDR | Ettus USRP B210 (USB 3.0) |
| Antenna | Telescopic whip antenna on the **RX2** port, shortened for the band (about 8 cm at 900 MHz, about 4 cm at 1.8 to 2.4 GHz) |
| Laptop | Dell Latitude 3410, Intel Core i7-10510U, 32 GB RAM, Ubuntu 22.04.5 LTS |

## Software

| Component | Version |
|---|---|
| Radioconda | 2025.03.14 |
| GNU Radio | 3.10.12 |
| UHD (USRP driver) | 4.8.0 |
| Python | 3.12.9 |

Radioconda installs into its own folder (`~/radioconda`) and does not change the system packages or other UHD versions on the laptop. See the FM receiver project for the install steps.

## Running

    source ~/radioconda/bin/activate
    cd ~/sdr_projects/03_spectrum_survey/grc
    python3 spectrum_survey.py

Or open `grc/spectrum_survey.grc` in GNU Radio Companion and use **Run > Generate**, then **Run > Execute**. When finished, close the window and run `conda deactivate`.

## Using the receiver

1. Connect the antenna to RX2 and the B210 to a USB 3.0 port.
2. Type a center frequency into the `freq` box (or drag the slider). The screen shows 20 MHz around it.
3. Open the Waterfall Display tab and click **Auto Scale**.
4. Start with `gain` at 30 to 40. Lower it if the spectrum looks flat-topped or shows ghost peaks.
5. Ignore the spike at the exact center (the radio's own leakage) and the outer 1 MHz on each side (filter roll-off).

## Bands to try

Downlink ranges. Operator carriers differ by area, so treat these as places to look.

| Band | Center to try | What to expect |
|---|---|---|
| Band 5 (850 MHz) | 880 MHz | LTE carrier |
| Band 8 (900 MHz) | 940 MHz | GSM or LTE |
| Band 3 (1800 MHz) | 1840 MHz | Several LTE carriers |
| Band 1 (2100 MHz) | 2140 MHz | LTE carriers |
| Band 40 TDD (2300 MHz) | 2350 MHz | Carriers that switch on and off in time stripes |
| Wi-Fi 2.4 GHz | 2437 MHz | Short bursts, channels 1, 6 and 11 |
| 5G n78 (3.3 to 3.8 GHz) | 3500 MHz | Wide 5G carriers |

## Flowgraph parameters

| Block | Setting |
|---|---|
| Sample rate | 20 MS/s |
| USRP Source | `type=b200`, antenna RX2, 1 channel, **Stream channels = `[]`** |
| QT GUI Sink | Complex input, FFT size 2048, center frequency = `freq`, bandwidth = `samp_rate` |
| `freq` slider | 700 MHz to 6 GHz, step 1 MHz |
| `gain` slider | 0 to 70 dB |

## Example observations

- At 880 MHz (870 to 890 MHz view): a flat-topped block about 9 to 10 MHz wide near 877 to 886 MHz, with bursty stripes. This is consistent with an LTE carrier in the Band 5 downlink.
- At 1840 MHz (1830 to 1850 MHz view): a strong narrow signal at about 1831.8 MHz in the Band 3 downlink.

The operator of a carrier cannot be told from a waterfall alone.

## Troubleshooting

| Problem | Fix |
|---|---|
| `Generate Error: invalid syntax` | Set the USRP Source block's **Stream channels** to `[]` |
| Waterfall looks like flat noise | Click **Auto Scale** |
| `O` characters in the terminal (overflow) | Use a USB 3.0 port, or set `samp_rate` to `10e6` |
| Device not found | Another program is using the B210. Close it and replug the radio |

## Repository layout

    03_spectrum_survey/
      README.md
      grc/spectrum_survey.grc     GNU Radio Companion flowgraph
      grc/spectrum_survey.py      generated Python script

Receive only. No transmission.

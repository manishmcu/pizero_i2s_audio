# Setting Up I2S Audio on Raspberry Pi Zero W

This guide explains how to enable and configure I2S audio on a Raspberry Pi Zero W using an external DAC like **PCM5102A** or **HifiBerry DAC**.

---

## **1. Wiring Connections**

### **Raspberry Pi Zero W → PCM5102A / HifiBerry DAC**
| **Raspberry Pi Zero W Pin** | **I2S DAC Pin** |
|----------------------------|----------------|
| Pin 6 (GND) | GND |
| Pin 1 (3.3V) or Pin 2 (5V) | VCC (Check DAC specs) |
| Pin 12 (GPIO18) | BCK (Bit Clock) |
| Pin 35 (GPIO19) | LCK (Left-Right Clock) |
| Pin 40 (GPIO21) | DIN (Data Input) |

---

## **2. Enable I2S on Raspberry Pi**

### **Modify boot configuration**
Edit the `config.txt` file:
```bash
sudo nano /boot/firmware/config.txt
```
Add the following lines at the end:
```
dtparam=audio=off
dtoverlay=hifiberry-dac
```
Save and exit (`CTRL + X`, then `Y`, then Enter`).

### **Reboot the Raspberry Pi**
```bash
sudo reboot
```

---

## **3. Verify I2S Audio Device**

After reboot, check if the DAC is detected:
```bash
aplay -l
```
Expected output:
```
card 1: sndrpihifiberry [snd_rpi_hifiberry_dac], device 0: HifiBerry DAC HiFi pcm5102a-hifi-0
```
If the I2S DAC does not appear, check wiring and re-enable the overlay.

---

## **4. Set I2S DAC as Default Audio Device**

Edit system-wide ALSA configuration:
```bash
sudo nano /etc/asound.conf
```
Add:
```
defaults.pcm.card 1
defaults.ctl.card 1
```
Save and exit, then reboot:
```bash
sudo reboot
```

---

## **5. Play Audio File**

### **Test with WAV File**
```bash
aplay music.wav
```
If there’s no sound, force playback on the correct device:
```bash
aplay -D plughw:1,0 music.wav
```

---

## **6. Fix Hissy or Distorted Sound**

### **Adjust Volume**
Run:
```bash
alsamixer
```
- Use arrow keys to **increase volume**.
- Press `M` to unmute.
- Press `ESC` to exit.

Save volume settings:
```bash
sudo alsactl store
```

### **Enable High-Quality Mode (Optional)**
Edit `config.txt`:
```bash
sudo nano /boot/firmware/config.txt
```
Add:
```
dtoverlay=hifiberry-dac,24db_digital_gain
```
Reboot:
```bash
sudo reboot
```

---

## **7. Troubleshooting**

### **No Sound Output?**
- Double-check wiring (especially **BCK, LCK, and DIN** connections).
- Ensure DAC is detected (`aplay -l` should list `snd_rpi_hifiberry_dac`).
- Use `alsamixer` to unmute and increase volume.
- Try forcing output with `aplay -D plughw:1,0 music.wav`.

### **Hissy or Low-Quality Sound?**
- Use high-quality mode (`24db_digital_gain` in `config.txt`).
- Ensure the **correct power supply** (some DACs require **5V instead of 3.3V**).
- Use `alsamixer` to set volume below 90% to avoid distortion.

---

## **Conclusion**
Following this guide, your Raspberry Pi Zero W should output high-quality audio via I2S. If issues persist, double-check **wiring, software configuration, and power supply.**


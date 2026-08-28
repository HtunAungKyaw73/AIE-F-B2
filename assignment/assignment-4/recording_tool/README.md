# Speech Training Recorder v0.9 (LU Lab., Myanmar) 🎤

A Python-based tool for recording prompted speech data, specifically designed for ASR/TTS corpus creation with Myanmar language support. This version is optimized for direct integration with Kaldi and ESPNet frameworks.

<p align="center">
<img src="https://github.com/ye-kyaw-thu/LU_Lab_Intern3_2025/blob/main/tools/recorder/recorder_UI.png" alt="UI" width="380"/>  
</p>  
<div align="center">
  Fig. UI of recorder  
</div> 

<br />  

## Features ✨

- **Speaker-Specific Recording**: Prompts for speaker name at startup and integrates it into filenames.
- **Kaldi/ESPNet Ready Output**: Automatically generates `wav.scp`, `text`, and `utt2spk` files alongside audio.
- **Kaldi-Compatible Filenames**: Files are named `speaker_timestamp.wav` (e.g., `student01_20260823_161922.wav`).
- **Multiple Prompt Modes**: 
  - Ordered (default: preserve original sequence, stops at end)
  - Sequential (cycles through the first N prompts infinitely)
  - Random (for ASR training)
- **Smart Output Handling**:
  - Auto-generated timestamped folders (e.g., `rec_1439_14Jun2025`)
  - WAV audio + enriched TSV metadata recording
- **Efficient Workflow**:
  - Keyboard shortcuts for all actions
  - Built-in playback verification
  - Automatic Myanmar font detection
  - **Previous Prompt Navigation**: Go back to fix mistakes without restarting.
  - **Unsaved Recording Protection**: Confirms before discarding unsaved audio.
- **Customizable Audio**:
  - Adjustable sample rates (8kHz-48kHz)
  - 16/32-bit depth options

## Installation 💻

```bash
pip install PyQt6 sounddevice numpy
```

## Usage 🚀

### Basic Recording

```bash
python recorder.py -p prompts.txt
```
*(Note: Defaults to "ordered" mode and 300 max prompts. You will be prompted to enter the speaker's name).*

### Advanced Options

```bash
# Sequential mode with auto-advance, limiting to 50 prompts
python recorder.py -p prompts.txt -m sequential -c 50 -a

# Custom output folder and high-quality audio (48kHz, 32-bit)
python recorder.py -p prompts.txt -d my_recordings -sr 48000 -b 32

# Random prompt selection for 100 prompts
python recorder.py -p prompts.txt -m random -c 100
```

### Full Help

```text
$ python recorder.py --help
usage: recorder.py [-h] [-p PROMPTS_FILENAME] [-d SAVE_DIR] [-m {random,ordered,sequential}] [-c PROMPTS_COUNT]
                   [-l PROMPT_LEN_SOFT_MAX] [-a] [-sr {8000,16000,44100,48000}] [-b {16,32}]

Speech Training Recorder (LU Lab., Myanmar) - Record prompted speech

options:
  -h, --help            show this help message and exit
  -p PROMPTS_FILENAME, --prompts_filename PROMPTS_FILENAME
                        text file containing prompts (one per line)
  -d SAVE_DIR, --save_dir SAVE_DIR
                        custom output directory (default: auto-generated, e.g., rec_1439_28Aug2026)
  -m {random,ordered,sequential}, --prompt_selection {random,ordered,sequential}
                        prompt selection mode (default: ordered)
                          ordered:    Goes through all prompts top-to-bottom and stops at the end.
                                    (Ignores -c count limit)
                          sequential: Takes the first N prompts (-c count) and loops them infinitely.
                          random:     Randomly selects N prompts (-c count) from the file.
  -c PROMPTS_COUNT, --prompts_count PROMPTS_COUNT
                        max prompts to use (default: 300). Used by 'sequential' and 'random' modes.
  -l PROMPT_LEN_SOFT_MAX, --prompt_len_soft_max PROMPT_LEN_SOFT_MAX
                        maximum prompt length in characters (0=no limit)
  -a, --auto_next       auto-advance to next prompt after save
  -sr {8000,16000,44100,48000}, --sample_rate {8000,16000,44100,48000}
                        sample rate in Hz (default: 16000)
  -b {16,32}, --bit_depth {16,32}
                        bit depth (16 or 32, default: 16)

Example usages:
  recorder.py -p isolated.txt -m ordered
  recorder.py -p script.txt -m ordered -d custom_folder
  recorder.py -p spoken.txt -m sequential -c 50
  recorder.py -p phrases.txt -m random -a
```

## Key Controls ⌨️  

| Action                  | Keyboard Shortcut | Button Label              |
|-------------------------|-------------------|---------------------------|
| Start/Stop Recording    | `Space`           | Start Recording (Space)   |
| Play Last Recording     | `P`               | Play Last (P)             |
| Save Recording          | `S`               | Save (S)                  |
| Previous Prompt         | `B`               | Previous Prompt (B)       |
| Next Prompt             | `N`               | Next Prompt (N)           |
| Delete Selected         | `Ctrl+D`          | Delete (Ctrl+D)           |
| Play Selected Recording | `Double-Click`    | (List Item Double-Click)  |

**Workflow Tip**: Always verify recordings with `P` before saving! If you make a mistake, just re-record and save the new one. You can delete the bad one later with `Ctrl+D`.

## Prompt File Format 📝  

Example `prompts.txt`: 

```
နေကောင်းလား
ထမင်း စားပြီးပြီလား
သတိရလို့ ဖုန်းလှမ်းခေါ်လိုက်တာပါ
ငါတို့ မတွေ့ဖြစ်တာတောင် ၅နှစ်ကျော်သွားပြီလားလို့
အလုပ်အကိုင်ကော အဆင်ပြေရဲ့လား
```

## Output Structure 📂  

The tool generates standard audio files, a detailed TSV file, and Kaldi-ready text files. If the speaker name is "student01", the output directory will look like this:

```
rec_1439_14Jun2025/
├── recordings.tsv
├── wav.scp                 <-- Kaldi audio mapping
├── text                    <-- Kaldi transcripts
├── utt2spk                  <-- Kaldi utterance to speaker mapping
├── student01_20260823_161922.wav
├── student01_20260823_161938.wav
└── ...
```

*(Note: You can pass this folder directly to Kaldi's `utils/fix_data_dir.sh` to automatically sort the files and generate `spk2utt`!)*

## Example TSV Metadata

The TSV file now includes `speaker` and `utt_id` columns for better tracking and direct mapping to Kaldi formats.

```
speaker	utt_id	filename	prompt	timestamp	sample_rate	bit_depth
student01	student01_20260823_161922	student01_20260823_161922.wav	ငါတို့ မတွေ့ဖြစ်တာတောင် ၅နှစ်ကျော်သွားပြီလားလို့	20260823_161922	16000	16
student01	student01_20260823_161938	student01_20260823_161938.wav	နေကောင်းလား	20260823_161938	16000	16
student01	student01_20260823_161900	student01_20260823_161900.wav	အလုပ်အကိုင်ကော အဆင်ပြေရဲ့လား	20260823_161900	16000	16
student01	student01_20260823_161915	student01_20260823_161915.wav	ထမင်း စားပြီးပြီလား	20260823_161915	16000	16
student01	student01_20260823_161933	student01_20260823_161933.wav	အလုပ်အကိုင်ကော အဆင်ပြေရဲ့လား	20260823_161933	16000	16
```

## Example Kaldi Output Files

**wav.scp**
```
student01_20260823_161922 /path/to/rec_1439_14Jun2025/student01_20260823_161922.wav
student01_20260823_161938 /path/to/rec_1439_14Jun2025/student01_20260823_161938.wav
```

**text**
```
student01_20260823_161922 ငါတို့ မတွေ့ဖြစ်တာတောင် ၅နှစ်ကျော်သွားပြီလားလို့
student01_20260823_161938 နေကောင်းလား
```

**utt2spk**
```
student01_20260823_161922 student01
student01_20260823_161938 student01
```

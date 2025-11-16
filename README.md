# digital signal processing for sound and music

coursework from the digital signal processing course at upf's music technology group, taught by prof. xavier serra. this repository documents my journey learning spectral processing techniques for audio analysis and synthesis.

[course website](https://www.upf.edu/web/smc/digital-signal-processing) | [sms-tools](https://github.com/MTG/sms-tools) | [music technology group](https://www.upf.edu/web/mtg)

## what's in this repository

### week 1: introduction to dsp
getting started with audio signal processing fundamentals. explored the basics of sampling, quantization, and the relationship between time and frequency domains.

### week 2: discrete fourier transform
implemented dft from scratch without using fft libraries. experimented with different window functions (hann, hamming, blackman) to understand spectral leakage. discovered that blackman window gives -58db sidelobes vs hann's -31db, which matters a lot for analyzing sounds with crowded spectra.

key insight: window size must contain 2-3 periods of the lowest frequency you want to analyze. for speech with f0=165hz, i used M=2047 samples.

### week 3: short-time fourier transform
moved from single-frame analysis to time-varying spectra. built spectrograms to visualize how frequency content evolves over time. learned that hop size controls temporal resolution - smaller hop = smoother time evolution but more computation.

experimented with different hop sizes (H=256, H=512, H=1024) on speech and music. found that H=512 gives good balance for most applications.

### week 4: sinusoidal model
modeled sounds as sums of sinusoids. implemented peak detection and partial tracking algorithms. synthesized sounds back from just the sinusoidal components.

worked with speech-female.wav and realized how well sinusoidal model captures voiced parts, but completely misses fricatives and plosives.

### week 5: harmonic model
extended sinusoidal model to enforce harmonic structure. implemented f0 estimation using two-way mismatch (twm) algorithm. tracked harmonic partials over time and used them for synthesis.

this is where it clicked: harmonic model is essentially sinusoidal model with the constraint that all frequencies are multiples of f0. works beautifully for pitched instruments and voice.

### week 6: stochastic model
modeled the residual (what's left after removing harmonics) as filtered white noise. computed spectral envelope of residual and used it to generate stochastic component.

key realization: stochastic model captures everything that's not tonal - breathiness in voice, attack transients in instruments, noise in recordings.

### week 7: harmonic plus stochastic (hps)
combined harmonic and stochastic models into one unified representation. this is the main synthesis model taught in the course.

implemented complete hps analysis/synthesis pipeline:
- estimate f0 and extract harmonics
- subtract resynthesized harmonics from original
- model residual stochastically
- resynthesize by adding harmonic + stochastic

tested on various sounds: speech, flute, violin, piano. hps works incredibly well for sustained sounds but struggles with percussive attacks.

### week 8: sound transformations
used spectral models for creative sound manipulation:
- time stretching: changed duration without affecting pitch
- pitch shifting: transposed frequency without changing duration  
- morphing: smooth interpolation between two sounds

implemented pitch shifter that works by scaling harmonic frequencies while keeping stochastic envelope. achieved reasonable quality for ±4 semitones.

## tools used

- python 3.10+
- [sms-tools](https://github.com/MTG/sms-tools) - xavier serra's spectral modeling toolkit
- numpy, scipy - numerical computing
- matplotlib - visualization
- librosa - audio i/o and utilities
- jupyter notebooks - experiments and analysis

## installation
```bash
# create virtual environment
python -m venv .venv
source .venv/bin/activate

# install dependencies
pip install numpy scipy matplotlib jupyter
pip install sms-tools librosa soundfile

# clone course materials
git clone https://github.com/MTG/sms-tools-materials.git
```

## key learnings

**on windowing:** the choice of window function is a trade-off between main lobe width (frequency resolution) and side lobe level (spectral leakage). there's no universal best window - it depends on your signal.

**on stft parameters:** three parameters control everything - window size M (frequency resolution), fft size N (frequency interpolation), hop size H (time resolution). these are not independent: M determines frequency resolution, H determines time resolution, N just interpolates between DFT bins.

**on model selection:** sinusoidal model works for pure tones, harmonic model for pitched sounds, stochastic model for noise, hps for most musical instruments and voice. but nothing works perfectly for everything - percussive sounds are still hard.

**on transformations:** time stretching and pitch shifting are essentially "tricking" the model - you analyze with one set of parameters and resynthesize with different ones. quality depends on how well the analysis model fits the sound.

## course context

this course is part of the [master in sound and music computing](https://www.upf.edu/web/smc) at universitat pompeu fabra. the program combines signal processing, machine learning, music perception, and creative applications.

term 1 (september - december 2025)  
5 ects credits  
taught by prof. xavier serra

other core courses in term 1:
- [machine learning for sound and music](https://github.com/lydiakrifka/machine-learning-2025)
- [music perception and cognition](https://github.com/lydiakrifka/music-perception-cognition-2025)
- [research methods](https://github.com/lydiakrifka/research-methods-2025)

---

maintained by lydia krifka-dobes  
academic year 2025-2026  
[music technology group, upf](https://www.upf.edu/web/mtg)

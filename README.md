# Data Augumentation For TextToSpeech
Introduce a method which do data augumentation for TTS AM model and Vocoder model training

I used this method to do data augmentation two years ago, I trained a few AM models, and Universal Vocoder model. It is useful. But almost all think the solution is too "野" to use. 

- If you AM/Vocoder model contain duration features, the method is useful
- If you AM/Vocoder model have fundmental frequency features, the method is useful
- If you Vocoder model is a Universal Vocoder, the method is useful
- If you want to simulate almost all range of ages and genders, the method is userful, train a source model, then refine with target speaker data, maybe  very very useful for one-shot TTS 
- If you want to enrich prosody related performances, the method is not your expected. 


# Details
  
If we have 48KHz recording wav files, and want to train 24KHz AM and Vocoder models, general solution is using tools (for example, sox) to convert the wav files from 48KHz to 24KHz, then extacting features -- in recently lastest models, the mel-spectrum feature is mostwide used.  And then train the AM and Vocoder model.

For Multi Speakers and Multi Languages, always follow the above ways too.  no matter how neural architecture the AM or Vocoder use. 

But how to create N time size data compare with the original 1 copy data.  this is the purpose/focus of this essay. And I hope this solution can become into "all your need method" for TTS. 

The solution is very simple and directly.  Suppose the original data is 48KHz.

1. Using sox to convert 48KHz into 24KHz, this is normal copy of 24KHz data, I name this copy speaker_24KHz.

2. 1 second 48KHz have 48000 sample value, if I save these 48000 sample value with samplerate 24KHz, then I have 2 second 24KHz audio content, I name this copy virtual_speaker_48KHz.
for example, load 48KHz with following code. 
    ```python
    src_sr = 48000
    data, sr = librosa.load(filename, sr=src_sr)
    ```
then save to 24KHz with 
  ```python
  import soundfile as sf
  tgt_sr = 24000
  sf.write('virtual48KHz.wav', data, tgt_sr)
  ```
  
3. Covert 48KHz wav file into 12KHz, 16KHz, 20KHz, 28KHz, 32KHz, 36KHz, 40KHz, 44KHz, with the method in 2, save all to 24KHz, will get virtual_speaker_12KHz ... virtual_speaker_44KHz.

You can use the above method to covert into any samplerate and save into 24KHz,  here I got 10 times data than original data. 

4. Train AM/Vocoder model. 

-For single speaker AM models, you should update single speaker model into multi speaker model, then use the augumented data train a 10 speakers model,
-For multi speaker AM models, the speaker count is 10 times larger. 
-For Vocoder, you can merge all data together to train a Universal Vocoder. 


# Others

1. Train a 48KHz Vocoder model may be a challenge work, but train a 24KHz Vocoder is easier. 
2. For production, if your product is 24Khz AM + Vocoder,  you can just use virutal_speaker_48KHz data to refine on 24KHz AM/Vocoder model, then update the model only,  this is the cheapest solution to support 48KHz. 


Have fun. 

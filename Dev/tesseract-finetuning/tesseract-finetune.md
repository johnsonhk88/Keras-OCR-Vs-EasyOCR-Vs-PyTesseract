1. Download lots of fonts (eg., `.ttf` files)
2. `git clone https://github.com/tesseract-ocr/tesstrain/`
3. `git clone https://github.com/tesseract-ocr/langdata_lstm`
4. Install Tesseract
5. Generate training data:
   ```
   cd src
   python -m tesstrain \
     --langdata_dir /path/to/langdata_lstm \
     --linedata_only \
     --fonts_dir /path/to/fonts \
     --lang <yourlang> \
     --maxpages <N> \
     --save_box_tiff \
     --distort_image \
     --fontlist '<your font 1> <your font 2> ...' \
     --output_dir train1
   ```
6. Split `train/*.training_files.txt` into two files `*.training_files.txt` and `*.eval_files.txt` (eg. 80 %, 20 % split)
7. Create language `.lstm` file: `combine_tessdata -e <yourlang>.traineddata /some/path/<yourlang>.lstm`
8. Train (eg. `<N> = 1000`):
   ```
   lstmtraining \
     --continue_from /some/path/<yourlang>.lstm \
     --model_output <your_new_model_name> \
     --traineddata /path/to/<yourlang>.traineddata \
     --train_listfile train/*.training_files.txt \
     --randomly_rotate \
     --max_iterations <N>
   ```
9. Eval:
   ```
   lstmeval \
     --eval_listfile train/*.eval_files.txt \
     --traineddata /path/to/<yourlang>.traineddata \
     --model <your_new_model_name>_checkpoint
   ```
   BCER = Character error rate, BWER = Word error rate
10. Loop 8. and 9. with increasing `<N>` until you are happy with the eval error rates.
11. ```
    lstmtraining \
      --stop_training \
      --continue_from <your_new_model_name>checkpoint \
      --traineddata /path/to/<yourlang>.traineddata \
      --model_output <your_new_model_name>.traineddata
    ```
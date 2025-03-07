# ICPR 2024 Competition on Claim Span Identification

## Disclaimer
- The dataset may contain data that, if viewed directly, might be offensive, insulting, threatening, or might otherwise cause anxiety
- The dataset may identify individuals (i.e., one or more natural persons), either directly or indirectly
- The dataset contain data that might be considered sensitive in any way (e.g., data that reveals race or ethnic origins, sexual orientations, religious beliefs, political opinions, etc.)

## Data Format
The English and Hindi sets of the data have been split into train **(~6k samples)** and validation sets **(500 samples)**. These splits are the stored in standard JSON files are present in the ```data``` directory. You may open the ```.json``` files in any text editor to visualist the data structure. 

Note that the usernames have been anonimyzed by giving them unique IDs.

On loading the data file (say with ```json.load()``` in python), it will return a list of dictionaries, one for each of the data points. Each dictionary has an ***"index"*** key (0, 1, 2, ...) and the following two important keys: ***"text_tokens"*** and ***"claims"***.

- The *"text_tokens"* contain a list of tokens that when joined form the text input. 
*Note that the output vectors (described below) for each data point need to be of the same size as the "text_tokens" list*

- The *"claims"* again contain a list of dictionaries, one for each of the disjoint claim-spans present in the corresponding text. An empty list denotes there are no claim spans in the text.
Each of these dictionaries contain the ***"start"*** and ***"end"*** indices of that particular claim-span in the text. 

*Note that the claim span-start index is inclusive, but the claim span-end index are exclusive and the indexing of tokens starts from 0. For example, if a span start and end is 3 and 7, and the text is "I read that mrna vaccines cause cancer !", then the claim is "mrna vaccines cause cancer" (i.e., consisting of the tokens indexed 3, 4, 5, 6).*


## Output Predictions Format
The output predictions file should again be a ```.json``` file, containing a list of lists, one list for each of the data points. Each of the interal lists should be the same size as the corresponding *"text_tokens"* (**0/1** for each of the tokens). The elements marked **1** denote that the corresponding token is part of a claim-span, and **0** denotes it is *NOT* part of any claims. 

For example, consider two texts -- 
- *"I  read  that  mrna  vaccines  cause  cancer  !"* 
- *"I  will  never  take  a  covid  vaccine  .  .  ."* 

Then the output JSON-file should look something like:
```
[
    [0 0 0 1 1 1 1 0], 
    [0 0 0 0 0 0 0 0 0 0]
]
```

*Note that the number of tokens generated by a model's tokenizer may not be equal to the actual number of text tokens. You may need to convert the predicted vectors so that the final output vectors are the same size as text_tokens.*


## Evaluation Metrics
We have calculated standard Macro-F1 and Jaccard scores for the individual data points, and then average them over all data points in the validation / test set.
You can run the following to print the scores:

```python  metrics.py  <path_to_input_data_file>  <path_to_output_preds_file>```

*Note: you may need to install "numpy" and "scikit-learn" libraries*. 
The input data file should be of the same format as given, and the output preds file should be of the format as described above.


## Scores on Validation set
As a baseline we used a basic [BERTforTokenClassification](https://huggingface.co/docs/transformers/en/model_doc/bert#transformers.BertForTokenClassification) model which can generate a class prediction (here 0/1) for each of the tokens in the text.

We loaded the model encoder with weights from [bert-base-multilingual-uncased](https://huggingface.co/google-bert/bert-base-multilingual-uncased) AND fine-tuned it on the respective train sets for 5 epochs. This model was achieved a score of 49.71 Jaccard and 74.09 M-F1 on the English validation set; and 65.64 Jaccard and 79.19 M-F1 on the Hindi validation set


## More Queries
For any further clarification, please write to [csi.icpr2024@gmail.com](mailto:csi.icpr2024@gmail.com)
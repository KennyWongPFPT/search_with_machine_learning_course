# For classifying product names to categories:

## What precision (P@1) were you able to achieve?

	$ ~/fastText-0.9.2/fasttext test model.bin test_data.txt
	N       9669
	P@1     0.655
	R@1     0.655

## What fastText parameters did you use?

	$ ~/fastText-0.9.2/fasttext supervised -input training_data.txt -output model -lr 1.0 -epoch 25 -wordNgrams 2

## How did you transform the product names?

	cat shuffled_labeled_products.txt | sed -e "s/\([.\!?,'/()]\)/ \1 /g" | tr "[:upper:]" "[:lower:]" | sed "s/[^[:alnum:]_]/ /g" | tr -s ' ' > normalized_labeled_products.txt

## How did you prune infrequent category labels, and how did that affect your precision?

	# labels with >500 products
	$ awk '{ print $1 }' labeled_products.txt | sort | uniq -c | sort -nk1 | tail -32 | awk '{ print $2 }'
	# get top labeled products
	$ while read label; do grep $label labeled_products.txt; done < top_labels.txt > pruned_labeled_products.txt

## How did you prune the category tree, and how did that affect your precision?

optional, skipped it

# For deriving synonyms from content:

## What were the results for your best model in the tokens used for evaluation?

	$ ~/fastText-0.9.2/fasttext nn title_model.bin
	Query word? freezer
	refrigerator 0.94447
	frost 0.943594
	free 0.934483
	dispenser 0.930599
	cu 0.928415
	inglis 0.917069
	ft 0.915892
	cleansteel 0.914464
	whirlpool 0.907179
	top 0.905555

## What fastText parameters did you use?

	$ ~/fastText-0.9.2/fasttext skipgram -input titles.txt -output -output title_model

## How did you transform the product names?

	$ cat titles.txt | sed -e "s/\([.\!?,'/()]\)/ \1 /g" | tr "[:upper:]" "[:lower:]" | sed "s/[^[:alnum:]]/ /g" | tr -s ' ' > normalized_titles.txt

# For integrating synonyms with search:

## How did you transform the product names (if different than previously)?

	#!/bin/bash
	
	while read word
	do
	echo $word | ~/fastText-0.9.2/fasttext nn title_model.bin | sed -e 's/Query word? //' |
	awk 'BEGIN { printf("%s", "'$word'") }
	{
		if ($2 > 0.7) {
			printf(",%s", $1)
		}
	}
	END { printf("\n") }'
	done < top_words.txt

## What threshold score did you use?

	0.7

## Were you able to find the additional results by matching synonyms?

no, reindex failed to work for me

# For classifying reviews:

optional, skipped

## What precision (P@1) were you able to achieve?

## What fastText parameters did you use?

## How did you transform the review content?

## What else did you try and learn?

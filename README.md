# END-3.0-Assignment-6
Assignment 6 of the END 3.0 course of The School Of AI

## Task 1
The following code was used for preparation of data :
```
# Turn a Unicode string to plain ASCII, thanks to
# https://stackoverflow.com/a/518232/2809427
def unicodeToAscii(s):
    return ''.join(
        c for c in unicodedata.normalize('NFD', s)
        if unicodedata.category(c) != 'Mn'
    )

# Lowercase, trim, and remove non-letter characters
def normalizeString(s):
    s = unicodeToAscii(s.lower().strip())
    s = re.sub(r"([.!?])", r" \1", s)
    s = re.sub(r"[0-9]+", r"NUM", s)                          # Since the data contains important dates and ages, added a NUM token for any dates/ages in the data
    s = re.sub(r"[^a-zA-Z.!?]+", r" ", s)
    return s
    
def readLangs(lang1, lang2, reverse=False):
    print("Reading lines...")

    # Read the file and split into lines
    # 3 files to read, added [1:] at the end to signify that we only need the data from second line (First line : Headers)
    lines = open('Question_Answer_Dataset_v1.2/S08/question_answer_pairs.txt', encoding='ISO-8859-1').read().strip().split('\n')[1:] \
    + open('Question_Answer_Dataset_v1.2/S09/question_answer_pairs.txt', encoding='ISO-8859-1').read().strip().split('\n')[1:] \
    + open('Question_Answer_Dataset_v1.2/S10/question_answer_pairs.txt', encoding='ISO-8859-1').read().strip().split('\n')[1:]

    # Split every line into pairs and normalize
    pairs = [line.split('\t')[1:3] for line in lines]          # We only need the question, answer pair (indices 1, 2) .. and not the other data on the line
    pairs = [[normalizeString(pair[0]), normalizeString(pair[1])] for pair in pairs if not (pair[0] == 'NULL' or pair[1] == 'NULL')]  # Discarding pairs where question or answer == 'NULL', and normalising the rest

    # Make Lang instances
    input_lang = Lang(lang1)
    output_lang = input_lang                                   # We share the lang object between input(questions) and output(answers) because both have same vocabulary

    return input_lang, output_lang, pairs
   
    
MAX_LENGTH = 100                                               # MAX_LENGTH 10 was too small.. the questions and answers in the dataset were long

def filterPair(p):
    return len(p[0].split(' ')) < MAX_LENGTH and \
        len(p[1].split(' ')) < MAX_LENGTH and \
        not p[0].startswith(("when"))                          # Discard all questions starting with when because the answer is almost always a number


def filterPairs(pairs):
    return [pair for pair in pairs if filterPair(pair)]
    
def prepareData(lang1, lang2, reverse=False):
    input_lang, output_lang, pairs = readLangs(lang1, lang2, reverse)
    print("Read %s sentence pairs" % len(pairs))
    pairs = filterPairs(pairs)
    print("Trimmed to %s sentence pairs" % len(pairs))
    print("Counting words...")
    for pair in pairs:
        input_lang.addSentence(pair[0])
        output_lang.addSentence(pair[1])
    print("Counted words:")
    print(input_lang.name, input_lang.n_words)
    print(output_lang.name, output_lang.n_words)
    return input_lang, output_lang, pairs


input_lang, output_lang, pairs = prepareData('eng', 'fra', True)
print(random.choice(pairs))
```

## Task 2
The following code was used for preparation of data :
```
# Turn a Unicode string to plain ASCII, thanks to
# https://stackoverflow.com/a/518232/2809427
def unicodeToAscii(s):
    return ''.join(
        c for c in unicodedata.normalize('NFD', s)
        if unicodedata.category(c) != 'Mn'
    )

# Lowercase, trim, and remove non-letter characters
def normalizeString(s):
    s = unicodeToAscii(s.lower().strip())
    s = re.sub(r"([.!?])", r" \1", s)
    s = re.sub(r"[^a-zA-Z.!?]+", r" ", s)
    return s

def readLangs(lang1, lang2, reverse=False):
    print("Reading lines...")

    # Read the file and split into lines
    data = pd.read_csv("quora_duplicate_questions.tsv", sep='\t')
    data = data.loc[data['is_duplicate'] == 1]
    

    # Split every line into pairs and normalize
    pairs = [[normalizeString(pair[0]), normalizeString(pair[1])] for pair in data.loc[::4,['question1', 'question2']].to_numpy().tolist()]   #Taking every 4th entry because it is taking very long to train, and colab is shutting down

    # Make Lang instances
    input_lang = Lang(lang1)
    output_lang = input_lang

    return input_lang, output_lang, pairs

MAX_LENGTH = 50

def filterPair(p):
    return len(p[0].split(' ')) < MAX_LENGTH and \
        len(p[1].split(' ')) < MAX_LENGTH


def filterPairs(pairs):
    return [pair for pair in pairs if filterPair(pair)]
    
def prepareData(lang1, lang2, reverse=False):
    input_lang, output_lang, pairs = readLangs(lang1, lang2, reverse)
    print("Read %s sentence pairs" % len(pairs))
    pairs = filterPairs(pairs)
    print("Trimmed to %s sentence pairs" % len(pairs))
    print("Counting words...")
    for pair in pairs:
        input_lang.addSentence(pair[0])
        output_lang.addSentence(pair[1])
    print("Counted words:")
    print(input_lang.name, input_lang.n_words)
    print(output_lang.name, output_lang.n_words)
    return input_lang, output_lang, pairs


input_lang, output_lang, pairs = prepareData('eng', 'fra', True)
print(random.choice(pairs))
```

Group 21 members : <br />

Mohammed Yaseen (47.yaseen@gmail.com)<br />
Mayank Singhal (singhal.mayank77@gmail.com)<br />
Ravi Vaishnav (ravivaishnav20@gmail.com)<br />
Sundeep Joshi<br />

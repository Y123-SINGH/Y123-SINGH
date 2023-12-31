  {
  "nbformat": 4,
  "nbformat_minor": 0,
  "metadata": {
    "colab": {
      "provenance": []
    },
    "kernelspec": {
      "name": "python3",
      "display_name": "Python 3"
    },
    "language_info": {
      "name": "python"
    },
    "gpuClass": "standard"
  },
  "cells": [
    {
      "cell_type": "code",
      "execution_count": 1,
      "metadata": {
        "id": "Qa_ONewhIVLB",
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "outputId": "7acfdd07-ca22-4611-fdea-6e64e7d6c0c5"
      },
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "Mounted at /gdrive\n",
            "/gdrive/MyDrive/project/Data_Extraction_and_NLP/TestAssignment\n"
          ]
        }
      ],
      "source": [
        "from google.colab import drive\n",
        "drive.mount('/gdrive')\n",
        "%cd '/gdrive/MyDrive/project/Data_Extraction_and_NLP/TestAssignment'"
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "#import necessary pacakages\n",
        "import requests\n",
        "from bs4 import BeautifulSoup\n",
        "import pandas as pd\n",
        "import os\n",
        "import nltk\n",
        "from nltk.tokenize import word_tokenize\n",
        "from nltk.corpus import stopwords\n",
        "nltk.download('punkt')\n",
        "nltk.download('stopwords')\n",
        "import re"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "3lNMH1gfF3cF",
        "outputId": "3474f221-df66-44ae-82b0-0123be61adae"
      },
      "execution_count": 26,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "[nltk_data] Downloading package punkt to /root/nltk_data...\n",
            "[nltk_data]   Package punkt is already up-to-date!\n",
            "[nltk_data] Downloading package stopwords to /root/nltk_data...\n",
            "[nltk_data]   Package stopwords is already up-to-date!\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "\n",
        "#read the url file into the pandas object\n",
        "df = pd.read_excel('Input.xlsx')\n",
        "\n",
        "#loop throgh each row in the df\n",
        "for index, row in df.iterrows():\n",
        "  url = row['URL']\n",
        "  url_id = row['URL_ID']\n",
        "\n",
        "  # make a request to url\n",
        "  header = {'User-Agent': \"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36\"}\n",
        "  try:\n",
        "    response = requests.get(url,headers=header)\n",
        "  except:\n",
        "    print(\"can't get response of {}\".format(url_id))\n",
        "\n",
        "  #create a beautifulsoup object\n",
        "  try:\n",
        "    soup = BeautifulSoup(response.content, 'html.parser')\n",
        "  except:\n",
        "    print(\"can't get page of {}\".format(url_id))\n",
        "  #find title\n",
        "  try:\n",
        "    title = soup.find('h1').get_text()\n",
        "  except:\n",
        "    print(\"can't get title of {}\".format(url_id))\n",
        "    continue\n",
        "  #find text\n",
        "  article = \"\"\n",
        "  try:\n",
        "    for p in soup.find_all('p'):\n",
        "      article += p.get_text()\n",
        "  except:\n",
        "    print(\"can't get text of {}\".format(url_id))\n",
        "\n",
        "  #write title and text to the file\n",
        "  file_name = '/gdrive/MyDrive/project/Data_Extraction_and_NLP/TestAssignment/TitleText/' + str(url_id) + '.txt'\n",
        "  with open(file_name, 'w') as file:\n",
        "    file.write(title + '\\n' + article)"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "AXXMNkOohRgr",
        "outputId": "0de27ec4-58a7-4a93-f0ff-e5df6ed9c1f3"
      },
      "execution_count": 23,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "can't get title of 44.0\n",
            "can't get title of 57.0\n",
            "can't get title of 144.0\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "\n",
        "# Directories\n",
        "text_dir = \"/gdrive/MyDrive/project/Data_Extraction_and_NLP/TestAssignment/TitleText\"\n",
        "stopwords_dir = \"/gdrive/MyDrive/project/Data_Extraction_and_NLP/TestAssignment/StopWords\"\n",
        "sentment_dir = \"/gdrive/MyDrive/project/Data_Extraction_and_NLP/TestAssignment/MasterDictionary\"\n",
        "\n",
        "# load all stop wors from the stopwords directory and store in the set variable\n",
        "stop_words = set()\n",
        "for files in os.listdir(stopwords_dir):\n",
        "  with open(os.path.join(stopwords_dir,files),'r',encoding='ISO-8859-1') as f:\n",
        "    stop_words.update(set(f.read().splitlines()))\n",
        "\n",
        "# load all text files  from the  directory and store in a list(docs)\n",
        "docs = []\n",
        "for text_file in os.listdir(text_dir):\n",
        "  with open(os.path.join(text_dir,text_file),'r') as f:\n",
        "    text = f.read()\n",
        "#tokenize the given text file\n",
        "    words = word_tokenize(text)\n",
        "# remove the stop words from the tokens\n",
        "    filtered_text = [word for word in words if word.lower() not in stop_words]\n",
        "# add each filtered tokens of each file into a list\n",
        "    docs.append(filtered_text)\n",
        "\n",
        "\n",
        "\n",
        "# store positive, Negative words from the directory\n",
        "pos=set()\n",
        "neg=set()\n",
        "\n",
        "for files in os.listdir(sentment_dir):\n",
        "  if files =='positive-words.txt':\n",
        "    with open(os.path.join(sentment_dir,files),'r',encoding='ISO-8859-1') as f:\n",
        "      pos.update(f.read().splitlines())\n",
        "  else:\n",
        "    with open(os.path.join(sentment_dir,files),'r',encoding='ISO-8859-1') as f:\n",
        "      neg.update(f.read().splitlines())\n",
        "\n",
        "# now collect the positive  and negative words from each file\n",
        "# calculate the scores from the positive and negative words \n",
        "positive_words = []\n",
        "Negative_words =[]\n",
        "positive_score = []\n",
        "negative_score = []\n",
        "polarity_score = []\n",
        "subjectivity_score = []\n",
        "\n",
        "#iterate through the list of docs\n",
        "for i in range(len(docs)):\n",
        "  positive_words.append([word for word in docs[i] if word.lower() in pos])\n",
        "  Negative_words.append([word for word in docs[i] if word.lower() in neg])\n",
        "  positive_score.append(len(positive_words[i]))\n",
        "  negative_score.append(len(Negative_words[i]))\n",
        "  polarity_score.append((positive_score[i] - negative_score[i]) / ((positive_score[i] + negative_score[i]) + 0.000001))\n",
        "  subjectivity_score.append((positive_score[i] + negative_score[i]) / ((len(docs[i])) + 0.000001))\n"
      ],
      "metadata": {
        "id": "1tRdSv8ErMOm"
      },
      "execution_count": 24,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "\n",
        "\n",
        "# Average Sentence Length = the number of words / the number of sentences\n",
        "# Percentage of Complex words = the number of complex words / the number of words \n",
        "# Fog Index = 0.4 * (Average Sentence Length + Percentage of Complex words)\n",
        "\n",
        "avg_sentence_length = []\n",
        "Percentage_of_Complex_words  =  []\n",
        "Fog_Index = []\n",
        "complex_word_count =  []\n",
        "avg_syllable_word_count =[]\n",
        "\n",
        "stopwords = set(stopwords.words('english'))\n",
        "def measure(file):\n",
        "  with open(os.path.join(text_dir, file),'r') as f:\n",
        "    text = f.read()\n",
        "# remove punctuations \n",
        "    text = re.sub(r'[^\\w\\s.]','',text)\n",
        "# split the given text file into sentences\n",
        "    sentences = text.split('.')\n",
        "# total number of sentences in a file\n",
        "    num_sentences = len(sentences)\n",
        "# total words in the file\n",
        "    words = [word  for word in text.split() if word.lower() not in stopwords ]\n",
        "    num_words = len(words)\n",
        " \n",
        "# complex words having syllable count is greater than 2\n",
        "# Complex words are words in the text that contain more than two syllables.\n",
        "    complex_words = []\n",
        "    for word in words:\n",
        "      vowels = 'aeiou'\n",
        "      syllable_count_word = sum( 1 for letter in word if letter.lower() in vowels)\n",
        "      if syllable_count_word > 2:\n",
        "        complex_words.append(word)\n",
        "\n",
        "# Syllable Count Per Word\n",
        "# We count the number of Syllables in each word of the text by counting the vowels present in each word.\n",
        "#  We also handle some exceptions like words ending with \"es\",\"ed\" by not counting them as a syllable.\n",
        "    syllable_count = 0\n",
        "    syllable_words =[]\n",
        "    for word in words:\n",
        "      if word.endswith('es'):\n",
        "        word = word[:-2]\n",
        "      elif word.endswith('ed'):\n",
        "        word = word[:-2]\n",
        "      vowels = 'aeiou'\n",
        "      syllable_count_word = sum( 1 for letter in word if letter.lower() in vowels)\n",
        "      if syllable_count_word >= 1:\n",
        "        syllable_words.append(word)\n",
        "        syllable_count += syllable_count_word\n",
        "\n",
        "\n",
        "    avg_sentence_len = num_words / num_sentences\n",
        "    avg_syllable_word_count = syllable_count / len(syllable_words)\n",
        "    Percent_Complex_words  =  len(complex_words) / num_words\n",
        "    Fog_Index = 0.4 * (avg_sentence_len + Percent_Complex_words)\n",
        "\n",
        "    return avg_sentence_len, Percent_Complex_words, Fog_Index, len(complex_words),avg_syllable_word_count\n",
        "\n",
        "# iterate through each file or doc\n",
        "for file in os.listdir(text_dir):\n",
        "  x,y,z,a,b = measure(file)\n",
        "  avg_sentence_length.append(x)\n",
        "  Percentage_of_Complex_words.append(y)\n",
        "  Fog_Index.append(z)\n",
        "  complex_word_count.append(a)\n",
        "  avg_syllable_word_count.append(b)"
      ],
      "metadata": {
        "id": "F8RaMuD_EnQQ"
      },
      "execution_count": 27,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "# Word Count and Average Word Length Sum of the total number of characters in each word/Total number of words\n",
        "# We count the total cleaned words present in the text by \n",
        "# removing the stop words (using stopwords class of nltk package).\n",
        "# removing any punctuations like ? ! , . from the word before counting.\n",
        "\n",
        "def cleaned_words(file):\n",
        "  with open(os.path.join(text_dir,file), 'r') as f:\n",
        "    text = f.read()\n",
        "    text = re.sub(r'[^\\w\\s]', '' , text)\n",
        "    words = [word  for word in text.split() if word.lower() not in stopwords]\n",
        "    length = sum(len(word) for word in words)\n",
        "    average_word_length = length / len(words)\n",
        "  return len(words),average_word_length\n",
        "\n",
        "word_count = []\n",
        "average_word_length = []\n",
        "for file in os.listdir(text_dir):\n",
        "  x, y = cleaned_words(file)\n",
        "  word_count.append(x)\n",
        "  average_word_length.append(y)\n",
        "\n",
        "\n",
        "# To calculate Personal Pronouns mentioned in the text, we use regex to find \n",
        "# the counts of the words - “I,” “we,” “my,” “ours,” and “us”. Special care is taken\n",
        "#  so that the country name US is not included in the list.\n",
        "def count_personal_pronouns(file):\n",
        "  with open(os.path.join(text_dir,file), 'r') as f:\n",
        "    text = f.read()\n",
        "    personal_pronouns = [\"I\", \"we\", \"my\", \"ours\", \"us\"]\n",
        "    count = 0\n",
        "    for pronoun in personal_pronouns:\n",
        "      count += len(re.findall(r\"\\b\" + pronoun + r\"\\b\", text)) # \\b is used to match word boundaries\n",
        "  return count\n",
        "\n",
        "pp_count = []\n",
        "for file in os.listdir(text_dir):\n",
        "  x = count_personal_pronouns(file)\n",
        "  pp_count.append(x)"
      ],
      "metadata": {
        "id": "4NElx7d94ICm"
      },
      "execution_count": 28,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "output_df = pd.read_excel('Output Data Structure.xlsx')\n",
        "\n",
        "# URL_ID 44 ,57, 144 does not exists i,e. page does not exist, throughs 404 error\n",
        "# so we are going to drop these rows from the table\n",
        "output_df.drop([44-37,57-37,144-37], axis = 0, inplace=True)\n",
        "\n",
        "# These are the required parameters \n",
        "variables = [positive_score,\n",
        "            negative_score,\n",
        "            polarity_score,\n",
        "            subjectivity_score,\n",
        "            avg_sentence_length,\n",
        "            Percentage_of_Complex_words,\n",
        "            Fog_Index,\n",
        "            avg_sentence_length,\n",
        "            complex_word_count,\n",
        "            word_count,\n",
        "            avg_syllable_word_count,\n",
        "            pp_count,\n",
        "            average_word_length]\n",
        "\n",
        "# write the values to the dataframe\n",
        "for i, var in enumerate(variables):\n",
        "  output_df.iloc[:,i+2] = var\n",
        "\n",
        "#now save the dataframe to the disk\n",
        "output_df.to_csv('Output_Data.csv')"
      ],
      "metadata": {
        "id": "mXsnVluZ9TG3"
      },
      "execution_count": 29,
      "outputs": []
    }
  ]
}
[31/12, 9:57 pm] 🕯️: # -*- coding: utf-8 -*-
"""Data Extraction and Text Analysis for Blackcoffer company.ipynb

Automatically generated by Colaboratory.

Original file is located at
    https://colab.research.google.com/drive/1VydH2zLceBk15MvImv-XjFxeBOS-FG5V
"""

# Commented out IPython magic to ensure Python compatibility.
from google.colab import drive
drive.mount('/gdrive')
# %cd '/gdrive/MyDrive/project/Data_Extraction_and_NLP/TestAssignment'

#import necessary pacakages
import requests
from bs4 import BeautifulSoup
import pandas as pd
import os
import nltk
from nltk.tokenize import word_tokenize
from nltk.corpus import stopwords
nltk.download('punkt')
nltk.download('stopwords')
import re

#read the url file into the pandas object
df = pd.read_excel('Input.xlsx')

#loop throgh each row in the df
for index, row in df.iterrows():
  url = row['URL']
  url_id = row['URL_ID']

  # make a request to url
  header = {'User-Agent': "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36"}
  try:
    response = requests.get(url,headers=header)
  except:
    print("can't get response of {}".format(url_id))

  #create a beautifulsoup object
  try:
    soup = BeautifulSoup(response.content, 'html.parser')
  except:
    print("can't get page of {}".format(url_id))
  #find title
  try:
    title = soup.find('h1').get_text()
  except:
    print("can't get title of {}".format(url_id))
    continue
  #find text
  article = ""
  try:
    for p in soup.find_all('p'):
      article += p.get_text()
  except:
    print("can't get text of {}".format(url_id))

  #write title and text to the file
  file_name = '/gdrive/MyDrive/project/Data_Extraction_and_NLP/TestAssignment/TitleText/' + str(url_id) + '.txt'
  with open(file_name, 'w') as file:
    file.write(title + '\n' + article)

# Directories
text_dir = "/gdrive/MyDrive/project/Data_Extraction_and_NLP/TestAssignment/TitleText"
stopwords_dir = "/gdrive/MyDrive/project/Data_Extraction_and_NLP/TestAssignment/StopWords"
sentment_dir = "/gdrive/MyDrive/project/Data_Extraction_and_NLP/TestAssignment/MasterDictionary"

# load all stop wors from the stopwords directory and store in the set variable
stop_words = set()
for files in os.listdir(stopwords_dir):
  with open(os.path.join(stopwords_dir,files),'r',encoding='ISO-8859-1') as f:
    stop_words.update(set(f.read().splitlines()))

# load all text files  from the  directory and store in a list(docs)
docs = []
for text_file in os.listdir(text_dir):
  with open(os.path.join(text_dir,text_file),'r') as f:
    text = f.read()
#tokenize the given text file
    words = word_tokenize(text)
# remove the stop words from the tokens
    filtered_text = [word for word in words if word.lower() not in stop_words]
# add each filtered tokens of each file into a list
    docs.append(filtered_text)



# store positive, Negative words from the directory
pos=set()
neg=set()

for files in os.listdir(sentment_dir):
  if files =='positive-words.txt':
    with open(os.path.join(sentment_dir,files),'r',encoding='ISO-8859-1') as f:
      pos.update(f.read().splitlines())
  else:
    with open(os.path.join(sentment_dir,files),'r',encoding='ISO-8859-1') as f:
      neg.update(f.read().splitlines())

# now collect the positive  and negative words from each file
# calculate the scores from the positive and negative words 
positive_words = []
Negative_words =[]
positive_score = []
negative_score = []
polarity_score = []
subjectivity_score = []

#iterate through the list of docs
for i in range(len(docs)):
  positive_words.append([word for word in docs[i] if word.lower() in pos])
  Negative_words.append([word for word in docs[i] if word.lower() in neg])
  positive_score.append(len(positive_words[i]))
  negative_score.append(len(Negative_words[i]))
  polarity_score.append((positive_score[i] - negative_score[i]) / ((positive_score[i] + negative_score[i]) + 0.000001))
  subjectivity_score.append((positive_score[i] + negative_score[i]) / ((len(docs[i])) + 0.000001))

# Average Sentence Length = the number of words / the number of sentences
# Percentage of Complex words = the number of complex words / the number of words 
# Fog Index = 0.4 * (Average Sentence Length + Percentage of Complex words)

avg_sentence_length = []
Percentage_of_Complex_words  =  []
Fog_Index = []
complex_word_count =  []
avg_syllable_word_count =[]

stopwords = set(stopwords.words('english'))
def measure(file):
  with open(os.path.join(text_dir, file),'r') as f:
    text = f.read()
# remove punctuations 
    text = re.sub(r'[^\w\s.]','',text)
# split the given text file into sentences
    sentences = text.split('.')
# total number of sentences in a file
    num_sentences = len(sentences)
# total words in the file
    words = [word  for word in text.split() if word.lower() not in stopwords ]
    num_words = len(words)
 
# complex words having syllable count is greater than 2
# Complex words are words in the text that contain more than two syllables.
    complex_words = []
    for word in words:
      vowels = 'aeiou'
      syllable_count_word = sum( 1 for letter in word if letter.lower() in vowels)
      if syllable_count_word > 2:
        complex_words.append(word)

# Syllable Count Per Word
# We count the number of Syllables in each word of the text by counting the vowels present in each word.
#  We also handle some exceptions like words ending with "es","ed" by not counting them as a syllable.
    syllable_count = 0
    syllable_words =[]
    for word in words:
      if word.endswith('es'):
        word = word[:-2]
      elif word.endswith('ed'):
        word = word[:-2]
      vowels = 'aeiou'
      syllable_count_word = sum( 1 for letter in word if letter.lower() in vowels)
      if syllable_count_word >= 1:
        syllable_words.append(word)
        syllable_count += syllable_count_word


    avg_sentence_len = num_words / num_sentences
    avg_syllable_word_count = syllable_count / len(syllable_words)
    Percent_Complex_words  =  len(complex_words) / num_words
    Fog_Index = 0.4 * (avg_sentence_len + Percent_Complex_words)

    return avg_sentence_len, Percent_Complex_words, Fog_Index, len(complex_words),avg_syllable_word_count

# iterate through each file or doc
for file in os.listdir(text_dir):
  x,y,z,a,b = measure(file)
  avg_sentence_length.append(x)
  Percentage_of_Complex_words.append(y)
  Fog_Index.append(z)
  complex_word_count.append(a)
  avg_syllable_word_count.append(b)

# Word Count and Average Word Length Sum of the total number of characters in each word/Total number of words
# We count the total cleaned words present in the text by 
# removing the stop words (using stopwords class of nltk package).
# removing any punctuations like ? ! , . from the word before counting.

def cleaned_words(file):
  with open(os.path.join(text_dir,file), 'r') as f:
    text = f.read()
    text = re.sub(r'[^\w\s]', '' , text)
    words = [word  for word in text.split() if word.lower() not in stopwords]
    length = sum(len(word) for word in words)
    average_word_length = length / len(words)
  return len(words),average_word_length

word_count = []
average_word_length = []
for file in os.listdir(text_dir):
  x, y = cleaned_words(file)
  word_count.append(x)
  average_word_length.append(y)


# To calculate Personal Pronouns mentioned in the text, we use regex to find 
# the counts of the words - “I,” “we,” “my,” “ours,” and “us”. Special care is taken
#  so that the country name US is not included in the list.
def count_personal_pronouns(file):
  with open(os.path.join(text_dir,file), 'r') as f:
    text = f.read()
    personal_pronouns = ["I", "we", "my", "ours", "us"]
    count = 0
    for pronoun in personal_pronouns:
      count += len(re.findall(r"\b" + pronoun + r"\b", text)) # \b is used to match word boundaries
  return count

pp_count = []
for file in os.listdir(text_dir):
  x = count_personal_pronouns(file)
  pp_count.append(x)

output_df = pd.read_excel('Output Data Structure.xlsx')

# URL_ID 44 ,57, 144 does not exists i,e. page does not exist, throughs 404 error
# so we are going to drop these rows from the table
output_df.drop([44-37,57-37,144-37], axis = 0, inplace=True)

# These are the required parameters 
variables = [positive_score,
            negative_score,
            polarity_score,
            subjectivity_score,
            avg_sentence_length,
            Percentage_of_Complex_words,
            Fog_Index,
            avg_sentence_length,
            complex_word_count,
            word_count,
            avg_syllable_word_count,
            pp_count,
            average_word_length]

# write the values to the dataframe
for i, var in enumerate(variables):
  output_df.iloc[:,i+2] = var

#now save the dataframe to the disk
output_df.to_csv('Output_Data.csv')

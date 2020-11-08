# ML-PROJECT{
  "nbformat": 4,
  "nbformat_minor": 0,
  "metadata": {
    "colab": {
      "name": "Copy of ML major project smart knower .ipynb",
      "provenance": []
    },
    "kernelspec": {
      "name": "python3",
      "display_name": "Python 3"
    }
  },
  "cells": [
    {
      "cell_type": "code",
      "metadata": {
        "id": "xUkkBgX7dYdC"
      },
      "source": [
        " \n",
        "#Completed By\n",
        "# 1. Srijan Prem Srivastava: Email- srijansriv@gmail.com\n",
        "# 2. G Vardhaman: Email- gajjalavardhaman99@gmail.com\n",
        "# 3. Ketki Churi: Email- ket28me@gmail.com\n",
        "# 4. Anand Krishnan B: Email- krisanofficial21@gmail.com"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "tnh7_3DxeBcu",
        "outputId": "b8cafe0b-8f3b-4b72-ff0f-ff256aac6859",
        "colab": {
          "base_uri": "https://localhost:8080/"
        }
      },
      "source": [
        "!pip install vaderSentiment"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "Collecting vaderSentiment\n",
            "\u001b[?25l  Downloading https://files.pythonhosted.org/packages/76/fc/310e16254683c1ed35eeb97386986d6c00bc29df17ce280aed64d55537e9/vaderSentiment-3.3.2-py2.py3-none-any.whl (125kB)\n",
            "\u001b[K     |████████████████████████████████| 133kB 2.7MB/s \n",
            "\u001b[?25hRequirement already satisfied: requests in /usr/local/lib/python3.6/dist-packages (from vaderSentiment) (2.23.0)\n",
            "Requirement already satisfied: certifi>=2017.4.17 in /usr/local/lib/python3.6/dist-packages (from requests->vaderSentiment) (2020.6.20)\n",
            "Requirement already satisfied: idna<3,>=2.5 in /usr/local/lib/python3.6/dist-packages (from requests->vaderSentiment) (2.10)\n",
            "Requirement already satisfied: urllib3!=1.25.0,!=1.25.1,<1.26,>=1.21.1 in /usr/local/lib/python3.6/dist-packages (from requests->vaderSentiment) (1.24.3)\n",
            "Requirement already satisfied: chardet<4,>=3.0.2 in /usr/local/lib/python3.6/dist-packages (from requests->vaderSentiment) (3.0.4)\n",
            "Installing collected packages: vaderSentiment\n",
            "Successfully installed vaderSentiment-3.3.2\n"
          ],
          "name": "stdout"
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "2sLIgMWoeOaH"
      },
      "source": [
        "from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer\n",
        "vs=SentimentIntensityAnalyzer()"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "ohC29Ec_eSox"
      },
      "source": [
        "\n",
        "import requests\n",
        "from bs4 import BeautifulSoup\n",
        "import pandas as pd\n",
        "import numpy as np\n",
        "import os"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "dSMJkv0_eXPR"
      },
      "source": [
        "urls= ['https://inshorts.com/en/read/sports',\n",
        "       'https://inshorts.com/en/read/world',\n",
        "       'https://inshorts.com/en/read/politics']"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "x6m7lmxXeltv"
      },
      "source": [
        "def build_dataset(urls):    \n",
        "  news_data= []\n",
        "  for url in urls:\n",
        "    news_category=url.split('/')[-1]\n",
        "    data=requests.get(url)\n",
        "    soup= BeautifulSoup(data.content)\n",
        "\n",
        "    news_articles=[{'news_headline':headline.find('span',attrs={\"itemprop\":\"headline\"}).string,\n",
        "                    'news_article':article.find('div',attrs={\"itemprop\":\"articleBody\"}).string,\n",
        "                    'news_category':news_category}\n",
        "                   \n",
        "                   for headline,article in zip(soup.find_all('div',class_=[\"news-card-title news-right-box\"]),\n",
        "                                               soup.find_all('div',class_=[\"news-card-content news-right-box\"]))]\n",
        "    news_articles =news_articles[0:20]\n",
        "    news_data.extend(news_articles)\n",
        "  df=pd.DataFrame(news_data)\n",
        "  return df"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "VHbAXi0beqFj"
      },
      "source": [
        "df=build_dataset(urls)\n",
        "df.head()"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "uJZ0ebMrfDTO"
      },
      "source": [
        "df.to_csv('news.csv',index=False)"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "a_kYqIZnidhi"
      },
      "source": [
        "import pandas as pd\n",
        "df=pd.read_csv('/content/news.csv')\n",
        "df"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "gWMNeni1iimv"
      },
      "source": [
        "#Pre-processing \n",
        "import nltk\n",
        "nltk.download('stopwords')\n",
        "stopword_list = nltk.corpus.stopwords.words('english')\n",
        "stopword_list.remove('not')\n",
        "stopword_list.remove('no')\n",
        "len(stopword_list)"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "lR-4wc2piu9h"
      },
      "source": [
        "def html_tag(text):\n",
        "  soup=BeautifulSoup(text,\"html.parser\")\n",
        "  new_text=soup.get_text()\n",
        "  return new_text\n"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "Fi-cjjy5izBO"
      },
      "source": [
        "!pip install contractions\n",
        "import contractions\n",
        "def con(text):\n",
        "  expand= contractions.fix(text)\n",
        "  return expand"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "O_AJKhoBi2lt"
      },
      "source": [
        "import re\n",
        "def remove_sp(text):\n",
        "  pattern = r'[^A-Za-z0-9\\s]'\n",
        "  text= re.sub(pattern,'',text)\n",
        "  return text"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "CLKwydcTi5Z_"
      },
      "source": [
        "from nltk.tokenize.toktok import ToktokTokenizer\n",
        "tokenizer = ToktokTokenizer()\n",
        "def remove_stopwords(text):\n",
        "  tokens= tokenizer.tokenize(text)\n",
        "  tokens= [token.strip() for token in tokens]\n",
        "  filtered_tokens=[token for token in tokens if token not in stopword_list]\n",
        "  filtered_text=' '.join(filtered_tokens)\n",
        "  return filtered_text"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "Wq5_I9L4i9g-"
      },
      "source": [
        "df.news_headline=df.news_headline.apply(lambda x:x.lower())\n",
        "df.news_article=df.news_article.apply(lambda x:x.lower())\n",
        "\n",
        "df.news_headline=df.news_headline.apply(html_tag)\n",
        "df.news_article=df.news_article.apply(html_tag)\n",
        "\n",
        "df.news_headline=df.news_headline.apply(con)\n",
        "df.news_article=df.news_article.apply(con)\n",
        "\n",
        "df.news_headline=df.news_headline.apply(remove_sp)\n",
        "df.news_article=df.news_article.apply(remove_sp)\n",
        "\n",
        "df.news_headline=df.news_headline.apply(remove_stopwords)\n",
        "df.news_article=df.news_article.apply(remove_stopwords)"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "Zi8U71k6jEY_"
      },
      "source": [
        "df['compound']= df['news_article'].apply(lambda x: vs.polarity_scores(x)['compound'])"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "S7oZvnc8jGKk"
      },
      "source": [
        "df.loc[df['compound'] >= 0.5, 'Sentiment'] = 'Positive'\n",
        "df.loc[df['compound'] <= -0.5, 'Sentiment'] = 'Negative'\n",
        "df.loc[(df['compound'] > -0.5) & (df['compound'] <0.5), 'Sentiment'] = 'Neutral'"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "WiXRVcjRjNue"
      },
      "source": [
        "df"
      ],
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "3gVhV_jxjSwV",
        "outputId": "38bddb4e-642f-4af4-c0c6-b92d11eb5c91",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 1000
        }
      },
      "source": [
        "#Deployment\n",
        "!pip install streamlit\n",
        "!pip install pyngrok\n",
        "from pyngrok import ngrok"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "Collecting streamlit\n",
            "\u001b[?25l  Downloading https://files.pythonhosted.org/packages/7d/64/59c5a06d567d27fda54a00db3789a05b50a4dd532df9260cadcff8dd4be9/streamlit-0.70.0-py2.py3-none-any.whl (7.4MB)\n",
            "\u001b[K     |████████████████████████████████| 7.4MB 2.7MB/s \n",
            "\u001b[?25hCollecting botocore>=1.13.44\n",
            "\u001b[?25l  Downloading https://files.pythonhosted.org/packages/99/40/b5e681d80dc46bafd0dc2e55266190cc432dfd5b72b9e7e1c5743aa6c362/botocore-1.19.13-py2.py3-none-any.whl (6.7MB)\n",
            "\u001b[K     |████████████████████████████████| 6.7MB 44.5MB/s \n",
            "\u001b[?25hCollecting validators\n",
            "  Downloading https://files.pythonhosted.org/packages/41/4a/3360ff3cf2b4a1b9721ac1fbff5f84663f41047d9874b3aa1ac82e862c44/validators-0.18.1-py3-none-any.whl\n",
            "Collecting boto3\n",
            "\u001b[?25l  Downloading https://files.pythonhosted.org/packages/85/54/099a2ea5d4b2d5931a26f280a7585f613b1fafaac9189e489a9e25004a01/boto3-1.16.13-py2.py3-none-any.whl (129kB)\n",
            "\u001b[K     |████████████████████████████████| 133kB 44.1MB/s \n",
            "\u001b[?25hCollecting enum-compat\n",
            "  Downloading https://files.pythonhosted.org/packages/55/ae/467bc4509246283bb59746e21a1a2f5a8aecbef56b1fa6eaca78cd438c8b/enum_compat-0.0.3-py3-none-any.whl\n",
            "Requirement already satisfied: pyarrow in /usr/local/lib/python3.6/dist-packages (from streamlit) (0.14.1)\n",
            "Collecting gitpython\n",
            "\u001b[?25l  Downloading https://files.pythonhosted.org/packages/24/d1/a7f8fe3df258549b303415157328bfcc63e9b11d06a7ad7a3327f3d32606/GitPython-3.1.11-py3-none-any.whl (159kB)\n",
            "\u001b[K     |████████████████████████████████| 163kB 42.3MB/s \n",
            "\u001b[?25hRequirement already satisfied: python-dateutil in /usr/local/lib/python3.6/dist-packages (from streamlit) (2.8.1)\n",
            "Requirement already satisfied: click>=7.0 in /usr/local/lib/python3.6/dist-packages (from streamlit) (7.1.2)\n",
            "Requirement already satisfied: packaging in /usr/local/lib/python3.6/dist-packages (from streamlit) (20.4)\n",
            "Collecting watchdog\n",
            "\u001b[?25l  Downloading https://files.pythonhosted.org/packages/0e/06/121302598a4fc01aca942d937f4a2c33430b7181137b35758913a8db10ad/watchdog-0.10.3.tar.gz (94kB)\n",
            "\u001b[K     |████████████████████████████████| 102kB 9.7MB/s \n",
            "\u001b[?25hRequirement already satisfied: tornado>=5.0 in /usr/local/lib/python3.6/dist-packages (from streamlit) (5.1.1)\n",
            "Requirement already satisfied: numpy in /usr/local/lib/python3.6/dist-packages (from streamlit) (1.18.5)\n",
            "Requirement already satisfied: altair>=3.2.0 in /usr/local/lib/python3.6/dist-packages (from streamlit) (4.1.0)\n",
            "Requirement already satisfied: pandas>=0.21.0 in /usr/local/lib/python3.6/dist-packages (from streamlit) (1.1.4)\n",
            "Collecting pydeck>=0.1.dev5\n",
            "\u001b[?25l  Downloading https://files.pythonhosted.org/packages/9e/9d/8fbf1f56cc5891e6c3295bf94fc176e9ab0a3ffdd090cc8b354ac2640f9a/pydeck-0.5.0-py2.py3-none-any.whl (4.5MB)\n",
            "\u001b[K     |████████████████████████████████| 4.5MB 41.7MB/s \n",
            "\u001b[?25hRequirement already satisfied: requests in /usr/local/lib/python3.6/dist-packages (from streamlit) (2.23.0)\n",
            "Requirement already satisfied: tzlocal in /usr/local/lib/python3.6/dist-packages (from streamlit) (1.5.1)\n",
            "Requirement already satisfied: pillow>=6.2.0 in /usr/local/lib/python3.6/dist-packages (from streamlit) (7.0.0)\n",
            "Collecting base58\n",
            "  Downloading https://files.pythonhosted.org/packages/3c/03/58572025c77b9e6027155b272a1b96298e711cd4f95c24967f7137ab0c4b/base58-2.0.1-py3-none-any.whl\n",
            "Requirement already satisfied: toml in /usr/local/lib/python3.6/dist-packages (from streamlit) (0.10.2)\n",
            "Requirement already satisfied: cachetools>=4.0 in /usr/local/lib/python3.6/dist-packages (from streamlit) (4.1.1)\n",
            "Requirement already satisfied: protobuf>=3.6.0 in /usr/local/lib/python3.6/dist-packages (from streamlit) (3.12.4)\n",
            "Collecting blinker\n",
            "\u001b[?25l  Downloading https://files.pythonhosted.org/packages/1b/51/e2a9f3b757eb802f61dc1f2b09c8c99f6eb01cf06416c0671253536517b6/blinker-1.4.tar.gz (111kB)\n",
            "\u001b[K     |████████████████████████████████| 112kB 35.3MB/s \n",
            "\u001b[?25hRequirement already satisfied: astor in /usr/local/lib/python3.6/dist-packages (from streamlit) (0.8.1)\n",
            "Collecting jmespath<1.0.0,>=0.7.1\n",
            "  Downloading https://files.pythonhosted.org/packages/07/cb/5f001272b6faeb23c1c9e0acc04d48eaaf5c862c17709d20e3469c6e0139/jmespath-0.10.0-py2.py3-none-any.whl\n",
            "Collecting urllib3<1.26,>=1.25.4; python_version != \"3.4\"\n",
            "\u001b[?25l  Downloading https://files.pythonhosted.org/packages/56/aa/4ef5aa67a9a62505db124a5cb5262332d1d4153462eb8fd89c9fa41e5d92/urllib3-1.25.11-py2.py3-none-any.whl (127kB)\n",
            "\u001b[K     |████████████████████████████████| 133kB 45.3MB/s \n",
            "\u001b[?25hRequirement already satisfied: six>=1.4.0 in /usr/local/lib/python3.6/dist-packages (from validators->streamlit) (1.15.0)\n",
            "Requirement already satisfied: decorator>=3.4.0 in /usr/local/lib/python3.6/dist-packages (from validators->streamlit) (4.4.2)\n",
            "Collecting s3transfer<0.4.0,>=0.3.0\n",
            "\u001b[?25l  Downloading https://files.pythonhosted.org/packages/69/79/e6afb3d8b0b4e96cefbdc690f741d7dd24547ff1f94240c997a26fa908d3/s3transfer-0.3.3-py2.py3-none-any.whl (69kB)\n",
            "\u001b[K     |████████████████████████████████| 71kB 6.9MB/s \n",
            "\u001b[?25hCollecting gitdb<5,>=4.0.1\n",
            "\u001b[?25l  Downloading https://files.pythonhosted.org/packages/48/11/d1800bca0a3bae820b84b7d813ad1eff15a48a64caea9c823fc8c1b119e8/gitdb-4.0.5-py3-none-any.whl (63kB)\n",
            "\u001b[K     |████████████████████████████████| 71kB 7.0MB/s \n",
            "\u001b[?25hRequirement already satisfied: pyparsing>=2.0.2 in /usr/local/lib/python3.6/dist-packages (from packaging->streamlit) (2.4.7)\n",
            "Collecting pathtools>=0.1.1\n",
            "  Downloading https://files.pythonhosted.org/packages/e7/7f/470d6fcdf23f9f3518f6b0b76be9df16dcc8630ad409947f8be2eb0ed13a/pathtools-0.1.2.tar.gz\n",
            "Requirement already satisfied: toolz in /usr/local/lib/python3.6/dist-packages (from altair>=3.2.0->streamlit) (0.11.1)\n",
            "Requirement already satisfied: jsonschema in /usr/local/lib/python3.6/dist-packages (from altair>=3.2.0->streamlit) (2.6.0)\n",
            "Requirement already satisfied: jinja2 in /usr/local/lib/python3.6/dist-packages (from altair>=3.2.0->streamlit) (2.11.2)\n",
            "Requirement already satisfied: entrypoints in /usr/local/lib/python3.6/dist-packages (from altair>=3.2.0->streamlit) (0.3)\n",
            "Requirement already satisfied: pytz>=2017.2 in /usr/local/lib/python3.6/dist-packages (from pandas>=0.21.0->streamlit) (2018.9)\n",
            "Requirement already satisfied: traitlets>=4.3.2 in /usr/local/lib/python3.6/dist-packages (from pydeck>=0.1.dev5->streamlit) (4.3.3)\n",
            "Collecting ipykernel>=5.1.2; python_version >= \"3.4\"\n",
            "\u001b[?25l  Downloading https://files.pythonhosted.org/packages/52/19/c2812690d8b340987eecd2cbc18549b1d130b94c5d97fcbe49f5f8710edf/ipykernel-5.3.4-py3-none-any.whl (120kB)\n",
            "\u001b[K     |████████████████████████████████| 122kB 37.9MB/s \n",
            "\u001b[?25hRequirement already satisfied: ipywidgets>=7.0.0 in /usr/local/lib/python3.6/dist-packages (from pydeck>=0.1.dev5->streamlit) (7.5.1)\n",
            "Requirement already satisfied: chardet<4,>=3.0.2 in /usr/local/lib/python3.6/dist-packages (from requests->streamlit) (3.0.4)\n",
            "Requirement already satisfied: certifi>=2017.4.17 in /usr/local/lib/python3.6/dist-packages (from requests->streamlit) (2020.6.20)\n",
            "Requirement already satisfied: idna<3,>=2.5 in /usr/local/lib/python3.6/dist-packages (from requests->streamlit) (2.10)\n",
            "Requirement already satisfied: setuptools in /usr/local/lib/python3.6/dist-packages (from protobuf>=3.6.0->streamlit) (50.3.2)\n",
            "Collecting smmap<4,>=3.0.1\n",
            "  Downloading https://files.pythonhosted.org/packages/b0/9a/4d409a6234eb940e6a78dfdfc66156e7522262f5f2fecca07dc55915952d/smmap-3.0.4-py2.py3-none-any.whl\n",
            "Requirement already satisfied: MarkupSafe>=0.23 in /usr/local/lib/python3.6/dist-packages (from jinja2->altair>=3.2.0->streamlit) (1.1.1)\n",
            "Requirement already satisfied: ipython-genutils in /usr/local/lib/python3.6/dist-packages (from traitlets>=4.3.2->pydeck>=0.1.dev5->streamlit) (0.2.0)\n",
            "Requirement already satisfied: ipython>=5.0.0 in /usr/local/lib/python3.6/dist-packages (from ipykernel>=5.1.2; python_version >= \"3.4\"->pydeck>=0.1.dev5->streamlit) (5.5.0)\n",
            "Requirement already satisfied: jupyter-client in /usr/local/lib/python3.6/dist-packages (from ipykernel>=5.1.2; python_version >= \"3.4\"->pydeck>=0.1.dev5->streamlit) (5.3.5)\n",
            "Requirement already satisfied: nbformat>=4.2.0 in /usr/local/lib/python3.6/dist-packages (from ipywidgets>=7.0.0->pydeck>=0.1.dev5->streamlit) (5.0.8)\n",
            "Requirement already satisfied: widgetsnbextension~=3.5.0 in /usr/local/lib/python3.6/dist-packages (from ipywidgets>=7.0.0->pydeck>=0.1.dev5->streamlit) (3.5.1)\n",
            "Requirement already satisfied: pickleshare in /usr/local/lib/python3.6/dist-packages (from ipython>=5.0.0->ipykernel>=5.1.2; python_version >= \"3.4\"->pydeck>=0.1.dev5->streamlit) (0.7.5)\n",
            "Requirement already satisfied: pexpect; sys_platform != \"win32\" in /usr/local/lib/python3.6/dist-packages (from ipython>=5.0.0->ipykernel>=5.1.2; python_version >= \"3.4\"->pydeck>=0.1.dev5->streamlit) (4.8.0)\n",
            "Requirement already satisfied: prompt-toolkit<2.0.0,>=1.0.4 in /usr/local/lib/python3.6/dist-packages (from ipython>=5.0.0->ipykernel>=5.1.2; python_version >= \"3.4\"->pydeck>=0.1.dev5->streamlit) (1.0.18)\n",
            "Requirement already satisfied: pygments in /usr/local/lib/python3.6/dist-packages (from ipython>=5.0.0->ipykernel>=5.1.2; python_version >= \"3.4\"->pydeck>=0.1.dev5->streamlit) (2.6.1)\n",
            "Requirement already satisfied: simplegeneric>0.8 in /usr/local/lib/python3.6/dist-packages (from ipython>=5.0.0->ipykernel>=5.1.2; python_version >= \"3.4\"->pydeck>=0.1.dev5->streamlit) (0.8.1)\n",
            "Requirement already satisfied: jupyter-core>=4.6.0 in /usr/local/lib/python3.6/dist-packages (from jupyter-client->ipykernel>=5.1.2; python_version >= \"3.4\"->pydeck>=0.1.dev5->streamlit) (4.6.3)\n",
            "Requirement already satisfied: pyzmq>=13 in /usr/local/lib/python3.6/dist-packages (from jupyter-client->ipykernel>=5.1.2; python_version >= \"3.4\"->pydeck>=0.1.dev5->streamlit) (19.0.2)\n",
            "Requirement already satisfied: notebook>=4.4.1 in /usr/local/lib/python3.6/dist-packages (from widgetsnbextension~=3.5.0->ipywidgets>=7.0.0->pydeck>=0.1.dev5->streamlit) (5.3.1)\n",
            "Requirement already satisfied: ptyprocess>=0.5 in /usr/local/lib/python3.6/dist-packages (from pexpect; sys_platform != \"win32\"->ipython>=5.0.0->ipykernel>=5.1.2; python_version >= \"3.4\"->pydeck>=0.1.dev5->streamlit) (0.6.0)\n",
            "Requirement already satisfied: wcwidth in /usr/local/lib/python3.6/dist-packages (from prompt-toolkit<2.0.0,>=1.0.4->ipython>=5.0.0->ipykernel>=5.1.2; python_version >= \"3.4\"->pydeck>=0.1.dev5->streamlit) (0.2.5)\n",
            "Requirement already satisfied: nbconvert in /usr/local/lib/python3.6/dist-packages (from notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets>=7.0.0->pydeck>=0.1.dev5->streamlit) (5.6.1)\n",
            "Requirement already satisfied: terminado>=0.8.1 in /usr/local/lib/python3.6/dist-packages (from notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets>=7.0.0->pydeck>=0.1.dev5->streamlit) (0.9.1)\n",
            "Requirement already satisfied: Send2Trash in /usr/local/lib/python3.6/dist-packages (from notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets>=7.0.0->pydeck>=0.1.dev5->streamlit) (1.5.0)\n",
            "Requirement already satisfied: bleach in /usr/local/lib/python3.6/dist-packages (from nbconvert->notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets>=7.0.0->pydeck>=0.1.dev5->streamlit) (3.2.1)\n",
            "Requirement already satisfied: defusedxml in /usr/local/lib/python3.6/dist-packages (from nbconvert->notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets>=7.0.0->pydeck>=0.1.dev5->streamlit) (0.6.0)\n",
            "Requirement already satisfied: testpath in /usr/local/lib/python3.6/dist-packages (from nbconvert->notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets>=7.0.0->pydeck>=0.1.dev5->streamlit) (0.4.4)\n",
            "Requirement already satisfied: mistune<2,>=0.8.1 in /usr/local/lib/python3.6/dist-packages (from nbconvert->notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets>=7.0.0->pydeck>=0.1.dev5->streamlit) (0.8.4)\n",
            "Requirement already satisfied: pandocfilters>=1.4.1 in /usr/local/lib/python3.6/dist-packages (from nbconvert->notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets>=7.0.0->pydeck>=0.1.dev5->streamlit) (1.4.3)\n",
            "Requirement already satisfied: webencodings in /usr/local/lib/python3.6/dist-packages (from bleach->nbconvert->notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets>=7.0.0->pydeck>=0.1.dev5->streamlit) (0.5.1)\n",
            "Building wheels for collected packages: watchdog, blinker, pathtools\n",
            "  Building wheel for watchdog (setup.py) ... \u001b[?25l\u001b[?25hdone\n",
            "  Created wheel for watchdog: filename=watchdog-0.10.3-cp36-none-any.whl size=73873 sha256=addd2b2e6206a9d74dcd4921d7143c927f1024539d5ad533b1785398b52af61c\n",
            "  Stored in directory: /root/.cache/pip/wheels/a8/1d/38/2c19bb311f67cc7b4d07a2ec5ea36ab1a0a0ea50db994a5bc7\n",
            "  Building wheel for blinker (setup.py) ... \u001b[?25l\u001b[?25hdone\n",
            "  Created wheel for blinker: filename=blinker-1.4-cp36-none-any.whl size=13450 sha256=3916ae116b4f2214b22809acdd06e244c361c23938bddeecfcd83f141d5916b5\n",
            "  Stored in directory: /root/.cache/pip/wheels/92/a0/00/8690a57883956a301d91cf4ec999cc0b258b01e3f548f86e89\n",
            "  Building wheel for pathtools (setup.py) ... \u001b[?25l\u001b[?25hdone\n",
            "  Created wheel for pathtools: filename=pathtools-0.1.2-cp36-none-any.whl size=8785 sha256=b5374ec68fb1c3a14d6b127075a5481212a8c165493e4c892c25397705fe4481\n",
            "  Stored in directory: /root/.cache/pip/wheels/0b/04/79/c3b0c3a0266a3cb4376da31e5bfe8bba0c489246968a68e843\n",
            "Successfully built watchdog blinker pathtools\n",
            "\u001b[31mERROR: google-colab 1.0.0 has requirement ipykernel~=4.10, but you'll have ipykernel 5.3.4 which is incompatible.\u001b[0m\n",
            "\u001b[31mERROR: datascience 0.10.6 has requirement folium==0.2.1, but you'll have folium 0.8.3 which is incompatible.\u001b[0m\n",
            "Installing collected packages: jmespath, urllib3, botocore, validators, s3transfer, boto3, enum-compat, smmap, gitdb, gitpython, pathtools, watchdog, ipykernel, pydeck, base58, blinker, streamlit\n",
            "  Found existing installation: urllib3 1.24.3\n",
            "    Uninstalling urllib3-1.24.3:\n",
            "      Successfully uninstalled urllib3-1.24.3\n",
            "  Found existing installation: ipykernel 4.10.1\n",
            "    Uninstalling ipykernel-4.10.1:\n",
            "      Successfully uninstalled ipykernel-4.10.1\n",
            "Successfully installed base58-2.0.1 blinker-1.4 boto3-1.16.13 botocore-1.19.13 enum-compat-0.0.3 gitdb-4.0.5 gitpython-3.1.11 ipykernel-5.3.4 jmespath-0.10.0 pathtools-0.1.2 pydeck-0.5.0 s3transfer-0.3.3 smmap-3.0.4 streamlit-0.70.0 urllib3-1.25.11 validators-0.18.1 watchdog-0.10.3\n"
          ],
          "name": "stdout"
        },
        {
          "output_type": "display_data",
          "data": {
            "application/vnd.colab-display-data+json": {
              "pip_warning": {
                "packages": [
                  "ipykernel",
                  "urllib3"
                ]
              }
            }
          },
          "metadata": {
            "tags": []
          }
        },
        {
          "output_type": "stream",
          "text": [
            "Collecting pyngrok\n",
            "  Downloading https://files.pythonhosted.org/packages/ea/63/e086f165125e9bf2e71c0db2955911baaaa0af8947ab5c7b3771bdf4d4d5/pyngrok-5.0.0.tar.gz\n",
            "Requirement already satisfied: PyYAML in /usr/local/lib/python3.6/dist-packages (from pyngrok) (3.13)\n",
            "Building wheels for collected packages: pyngrok\n",
            "  Building wheel for pyngrok (setup.py) ... \u001b[?25l\u001b[?25hdone\n",
            "  Created wheel for pyngrok: filename=pyngrok-5.0.0-cp36-none-any.whl size=18780 sha256=cfd7e9d2de9bf63bc0c1b652a14f889ab5811be8f7db39433120316c9b058054\n",
            "  Stored in directory: /root/.cache/pip/wheels/95/df/23/af8dde08c3fcdc7b966adcacef48ab29aa3b0b1860df5d2b79\n",
            "Successfully built pyngrok\n",
            "Installing collected packages: pyngrok\n",
            "Successfully installed pyngrok-5.0.0\n"
          ],
          "name": "stdout"
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "2xS-HuYOjUEN",
        "outputId": "e16c5f2e-6082-4b20-a534-cdaaa13df164",
        "colab": {
          "base_uri": "https://localhost:8080/"
        }
      },
      "source": [
        "%%writefile app.py\n",
        "import streamlit as st\n",
        "import numpy as np\n",
        "from skimage.io import imread\n",
        "from skimage.transform import resize\n",
        "st.title('Sentiment analysis using vader sentiment')"
      ],
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "text": [
            "Writing app.py\n"
          ],
          "name": "stdout"
        }
      ]
    },
    {
      "cell_type": "code",
      "metadata": {
        "id": "YaFlRLEsjl8c"
      },
      "source": [
        "!nohup streamlit run app.py &\n",
        "url=ngrok.connect(port='8501')\n",
        "url"
      ],
      "execution_count": null,
      "outputs": []
    }
  ]
}

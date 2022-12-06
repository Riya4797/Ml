# Ml{
  "nbformat": 4,
  "nbformat_minor": 0,
  "metadata": {
    "colab": {
      "provenance": [],
      "mount_file_id": "1QoJYAE30xB7qXepPwAHC-YBEMp-aUeIC",
      "authorship_tag": "ABX9TyP+YDFZ46VGa3IOTsB7NY/6",
      "include_colab_link": true
    },
    "kernelspec": {
      "name": "python3",
      "display_name": "Python 3"
    },
    "language_info": {
      "name": "python"
    },
    "accelerator": "GPU",
    "gpuClass": "standard"
  },
  "cells": [
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "view-in-github",
        "colab_type": "text"
      },
      "source": [
        "<a href=\"https://colab.research.google.com/github/Mathurshab2210/Ml_with_Mathur/blob/main/Fakenews.ipynb\" target=\"_parent\"><img src=\"https://colab.research.google.com/assets/colab-badge.svg\" alt=\"Open In Colab\"/></a>"
      ]
    },
    {
      "cell_type": "code",
      "execution_count": 2,
      "metadata": {
        "id": "2bXUTw76lkXz"
      },
      "outputs": [],
      "source": [
        "import pandas as pd\n",
        "import numpy as np\n",
        "import re# to search the words in doc\n",
        "from nltk.corpus import stopwords\n",
        "from nltk.stem.porter import PorterStemmer\n",
        "from sklearn.feature_extraction.text import TfidfVectorizer \n",
        "from sklearn.model_selection import train_test_split\n",
        "from sklearn.linear_model import LogisticRegression\n",
        "from sklearn.metrics import accuracy_score"
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "import nltk\n",
        "nltk.download('stopwords')"
      ],
      "metadata": {
        "id": "R0WzRPV9neXR",
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "outputId": "730e946b-d680-4546-8073-e7b049963d8d"
      },
      "execution_count": 3,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "[nltk_data] Downloading package stopwords to /root/nltk_data...\n",
            "[nltk_data]   Package stopwords is already up-to-date!\n"
          ]
        },
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "True"
            ]
          },
          "metadata": {},
          "execution_count": 3
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "print(stopwords.words('english'))"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "m_J_VeD06ipo",
        "outputId": "32212a8a-4592-449e-cf0c-1e8e4465c473"
      },
      "execution_count": 4,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "['i', 'me', 'my', 'myself', 'we', 'our', 'ours', 'ourselves', 'you', \"you're\", \"you've\", \"you'll\", \"you'd\", 'your', 'yours', 'yourself', 'yourselves', 'he', 'him', 'his', 'himself', 'she', \"she's\", 'her', 'hers', 'herself', 'it', \"it's\", 'its', 'itself', 'they', 'them', 'their', 'theirs', 'themselves', 'what', 'which', 'who', 'whom', 'this', 'that', \"that'll\", 'these', 'those', 'am', 'is', 'are', 'was', 'were', 'be', 'been', 'being', 'have', 'has', 'had', 'having', 'do', 'does', 'did', 'doing', 'a', 'an', 'the', 'and', 'but', 'if', 'or', 'because', 'as', 'until', 'while', 'of', 'at', 'by', 'for', 'with', 'about', 'against', 'between', 'into', 'through', 'during', 'before', 'after', 'above', 'below', 'to', 'from', 'up', 'down', 'in', 'out', 'on', 'off', 'over', 'under', 'again', 'further', 'then', 'once', 'here', 'there', 'when', 'where', 'why', 'how', 'all', 'any', 'both', 'each', 'few', 'more', 'most', 'other', 'some', 'such', 'no', 'nor', 'not', 'only', 'own', 'same', 'so', 'than', 'too', 'very', 's', 't', 'can', 'will', 'just', 'don', \"don't\", 'should', \"should've\", 'now', 'd', 'll', 'm', 'o', 're', 've', 'y', 'ain', 'aren', \"aren't\", 'couldn', \"couldn't\", 'didn', \"didn't\", 'doesn', \"doesn't\", 'hadn', \"hadn't\", 'hasn', \"hasn't\", 'haven', \"haven't\", 'isn', \"isn't\", 'ma', 'mightn', \"mightn't\", 'mustn', \"mustn't\", 'needn', \"needn't\", 'shan', \"shan't\", 'shouldn', \"shouldn't\", 'wasn', \"wasn't\", 'weren', \"weren't\", 'won', \"won't\", 'wouldn', \"wouldn't\"]\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "news=pd.read_csv('/content/drive/MyDrive/CNN/train.csv')"
      ],
      "metadata": {
        "id": "DltvEJiV6yDr"
      },
      "execution_count": 5,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "news.describe()"
      ],
      "metadata": {
        "id": "d04kuE9370gm",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 300
        },
        "outputId": "bd240b89-7da5-447d-d970-13ad3a6505cb"
      },
      "execution_count": 6,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "                 id         label\n",
              "count  20800.000000  20800.000000\n",
              "mean   10399.500000      0.500625\n",
              "std     6004.587135      0.500012\n",
              "min        0.000000      0.000000\n",
              "25%     5199.750000      0.000000\n",
              "50%    10399.500000      1.000000\n",
              "75%    15599.250000      1.000000\n",
              "max    20799.000000      1.000000"
            ],
            "text/html": [
              "\n",
              "  <div id=\"df-b2bdd81d-762a-4f15-8377-12363c0a21e1\">\n",
              "    <div class=\"colab-df-container\">\n",
              "      <div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>id</th>\n",
              "      <th>label</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>count</th>\n",
              "      <td>20800.000000</td>\n",
              "      <td>20800.000000</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>mean</th>\n",
              "      <td>10399.500000</td>\n",
              "      <td>0.500625</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>std</th>\n",
              "      <td>6004.587135</td>\n",
              "      <td>0.500012</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>min</th>\n",
              "      <td>0.000000</td>\n",
              "      <td>0.000000</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>25%</th>\n",
              "      <td>5199.750000</td>\n",
              "      <td>0.000000</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>50%</th>\n",
              "      <td>10399.500000</td>\n",
              "      <td>1.000000</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>75%</th>\n",
              "      <td>15599.250000</td>\n",
              "      <td>1.000000</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>max</th>\n",
              "      <td>20799.000000</td>\n",
              "      <td>1.000000</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "</div>\n",
              "      <button class=\"colab-df-convert\" onclick=\"convertToInteractive('df-b2bdd81d-762a-4f15-8377-12363c0a21e1')\"\n",
              "              title=\"Convert this dataframe to an interactive table.\"\n",
              "              style=\"display:none;\">\n",
              "        \n",
              "  <svg xmlns=\"http://www.w3.org/2000/svg\" height=\"24px\"viewBox=\"0 0 24 24\"\n",
              "       width=\"24px\">\n",
              "    <path d=\"M0 0h24v24H0V0z\" fill=\"none\"/>\n",
              "    <path d=\"M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z\"/><path d=\"M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z\"/>\n",
              "  </svg>\n",
              "      </button>\n",
              "      \n",
              "  <style>\n",
              "    .colab-df-container {\n",
              "      display:flex;\n",
              "      flex-wrap:wrap;\n",
              "      gap: 12px;\n",
              "    }\n",
              "\n",
              "    .colab-df-convert {\n",
              "      background-color: #E8F0FE;\n",
              "      border: none;\n",
              "      border-radius: 50%;\n",
              "      cursor: pointer;\n",
              "      display: none;\n",
              "      fill: #1967D2;\n",
              "      height: 32px;\n",
              "      padding: 0 0 0 0;\n",
              "      width: 32px;\n",
              "    }\n",
              "\n",
              "    .colab-df-convert:hover {\n",
              "      background-color: #E2EBFA;\n",
              "      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);\n",
              "      fill: #174EA6;\n",
              "    }\n",
              "\n",
              "    [theme=dark] .colab-df-convert {\n",
              "      background-color: #3B4455;\n",
              "      fill: #D2E3FC;\n",
              "    }\n",
              "\n",
              "    [theme=dark] .colab-df-convert:hover {\n",
              "      background-color: #434B5C;\n",
              "      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);\n",
              "      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));\n",
              "      fill: #FFFFFF;\n",
              "    }\n",
              "  </style>\n",
              "\n",
              "      <script>\n",
              "        const buttonEl =\n",
              "          document.querySelector('#df-b2bdd81d-762a-4f15-8377-12363c0a21e1 button.colab-df-convert');\n",
              "        buttonEl.style.display =\n",
              "          google.colab.kernel.accessAllowed ? 'block' : 'none';\n",
              "\n",
              "        async function convertToInteractive(key) {\n",
              "          const element = document.querySelector('#df-b2bdd81d-762a-4f15-8377-12363c0a21e1');\n",
              "          const dataTable =\n",
              "            await google.colab.kernel.invokeFunction('convertToInteractive',\n",
              "                                                     [key], {});\n",
              "          if (!dataTable) return;\n",
              "\n",
              "          const docLinkHtml = 'Like what you see? Visit the ' +\n",
              "            '<a target=\"_blank\" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'\n",
              "            + ' to learn more about interactive tables.';\n",
              "          element.innerHTML = '';\n",
              "          dataTable['output_type'] = 'display_data';\n",
              "          await google.colab.output.renderOutput(dataTable, element);\n",
              "          const docLink = document.createElement('div');\n",
              "          docLink.innerHTML = docLinkHtml;\n",
              "          element.appendChild(docLink);\n",
              "        }\n",
              "      </script>\n",
              "    </div>\n",
              "  </div>\n",
              "  "
            ]
          },
          "metadata": {},
          "execution_count": 6
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "from google.colab import drive\n",
        "drive.mount('/content/drive')"
      ],
      "metadata": {
        "id": "cp6TP6ux9B_A",
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "outputId": "63e6bdb9-65fc-4803-d4bb-5c0bd87a15da"
      },
      "execution_count": 7,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "Drive already mounted at /content/drive; to attempt to forcibly remount, call drive.mount(\"/content/drive\", force_remount=True).\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "news.shape"
      ],
      "metadata": {
        "id": "khda1V8XyuPX",
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "outputId": "d8e47630-28b7-4119-de31-478c737cc9d7"
      },
      "execution_count": 8,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "(20800, 5)"
            ]
          },
          "metadata": {},
          "execution_count": 8
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "news.head()"
      ],
      "metadata": {
        "id": "OOIJiNYhywxO",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 206
        },
        "outputId": "979a9429-74db-408d-a592-d6eff1d039d4"
      },
      "execution_count": 9,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "   id                                              title              author  \\\n",
              "0   0  House Dem Aide: We Didn’t Even See Comey’s Let...       Darrell Lucus   \n",
              "1   1  FLYNN: Hillary Clinton, Big Woman on Campus - ...     Daniel J. Flynn   \n",
              "2   2                  Why the Truth Might Get You Fired  Consortiumnews.com   \n",
              "3   3  15 Civilians Killed In Single US Airstrike Hav...     Jessica Purkiss   \n",
              "4   4  Iranian woman jailed for fictional unpublished...      Howard Portnoy   \n",
              "\n",
              "                                                text  label  \n",
              "0  House Dem Aide: We Didn’t Even See Comey’s Let...      1  \n",
              "1  Ever get the feeling your life circles the rou...      0  \n",
              "2  Why the Truth Might Get You Fired October 29, ...      1  \n",
              "3  Videos 15 Civilians Killed In Single US Airstr...      1  \n",
              "4  Print \\nAn Iranian woman has been sentenced to...      1  "
            ],
            "text/html": [
              "\n",
              "  <div id=\"df-14da148c-3af3-47b2-816d-42f352df6def\">\n",
              "    <div class=\"colab-df-container\">\n",
              "      <div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>id</th>\n",
              "      <th>title</th>\n",
              "      <th>author</th>\n",
              "      <th>text</th>\n",
              "      <th>label</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>0</td>\n",
              "      <td>House Dem Aide: We Didn’t Even See Comey’s Let...</td>\n",
              "      <td>Darrell Lucus</td>\n",
              "      <td>House Dem Aide: We Didn’t Even See Comey’s Let...</td>\n",
              "      <td>1</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>1</td>\n",
              "      <td>FLYNN: Hillary Clinton, Big Woman on Campus - ...</td>\n",
              "      <td>Daniel J. Flynn</td>\n",
              "      <td>Ever get the feeling your life circles the rou...</td>\n",
              "      <td>0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>2</td>\n",
              "      <td>Why the Truth Might Get You Fired</td>\n",
              "      <td>Consortiumnews.com</td>\n",
              "      <td>Why the Truth Might Get You Fired October 29, ...</td>\n",
              "      <td>1</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>3</td>\n",
              "      <td>15 Civilians Killed In Single US Airstrike Hav...</td>\n",
              "      <td>Jessica Purkiss</td>\n",
              "      <td>Videos 15 Civilians Killed In Single US Airstr...</td>\n",
              "      <td>1</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>4</td>\n",
              "      <td>Iranian woman jailed for fictional unpublished...</td>\n",
              "      <td>Howard Portnoy</td>\n",
              "      <td>Print \\nAn Iranian woman has been sentenced to...</td>\n",
              "      <td>1</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "</div>\n",
              "      <button class=\"colab-df-convert\" onclick=\"convertToInteractive('df-14da148c-3af3-47b2-816d-42f352df6def')\"\n",
              "              title=\"Convert this dataframe to an interactive table.\"\n",
              "              style=\"display:none;\">\n",
              "        \n",
              "  <svg xmlns=\"http://www.w3.org/2000/svg\" height=\"24px\"viewBox=\"0 0 24 24\"\n",
              "       width=\"24px\">\n",
              "    <path d=\"M0 0h24v24H0V0z\" fill=\"none\"/>\n",
              "    <path d=\"M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z\"/><path d=\"M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z\"/>\n",
              "  </svg>\n",
              "      </button>\n",
              "      \n",
              "  <style>\n",
              "    .colab-df-container {\n",
              "      display:flex;\n",
              "      flex-wrap:wrap;\n",
              "      gap: 12px;\n",
              "    }\n",
              "\n",
              "    .colab-df-convert {\n",
              "      background-color: #E8F0FE;\n",
              "      border: none;\n",
              "      border-radius: 50%;\n",
              "      cursor: pointer;\n",
              "      display: none;\n",
              "      fill: #1967D2;\n",
              "      height: 32px;\n",
              "      padding: 0 0 0 0;\n",
              "      width: 32px;\n",
              "    }\n",
              "\n",
              "    .colab-df-convert:hover {\n",
              "      background-color: #E2EBFA;\n",
              "      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);\n",
              "      fill: #174EA6;\n",
              "    }\n",
              "\n",
              "    [theme=dark] .colab-df-convert {\n",
              "      background-color: #3B4455;\n",
              "      fill: #D2E3FC;\n",
              "    }\n",
              "\n",
              "    [theme=dark] .colab-df-convert:hover {\n",
              "      background-color: #434B5C;\n",
              "      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);\n",
              "      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));\n",
              "      fill: #FFFFFF;\n",
              "    }\n",
              "  </style>\n",
              "\n",
              "      <script>\n",
              "        const buttonEl =\n",
              "          document.querySelector('#df-14da148c-3af3-47b2-816d-42f352df6def button.colab-df-convert');\n",
              "        buttonEl.style.display =\n",
              "          google.colab.kernel.accessAllowed ? 'block' : 'none';\n",
              "\n",
              "        async function convertToInteractive(key) {\n",
              "          const element = document.querySelector('#df-14da148c-3af3-47b2-816d-42f352df6def');\n",
              "          const dataTable =\n",
              "            await google.colab.kernel.invokeFunction('convertToInteractive',\n",
              "                                                     [key], {});\n",
              "          if (!dataTable) return;\n",
              "\n",
              "          const docLinkHtml = 'Like what you see? Visit the ' +\n",
              "            '<a target=\"_blank\" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'\n",
              "            + ' to learn more about interactive tables.';\n",
              "          element.innerHTML = '';\n",
              "          dataTable['output_type'] = 'display_data';\n",
              "          await google.colab.output.renderOutput(dataTable, element);\n",
              "          const docLink = document.createElement('div');\n",
              "          docLink.innerHTML = docLinkHtml;\n",
              "          element.appendChild(docLink);\n",
              "        }\n",
              "      </script>\n",
              "    </div>\n",
              "  </div>\n",
              "  "
            ]
          },
          "metadata": {},
          "execution_count": 9
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        " #missing value analysis\n",
        " news.isnull().sum()"
      ],
      "metadata": {
        "id": "lU0dA9fpy1t9",
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "outputId": "c4cc7dd0-c409-4078-afff-e633a48fb8ec"
      },
      "execution_count": 10,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "id           0\n",
              "title      558\n",
              "author    1957\n",
              "text        39\n",
              "label        0\n",
              "dtype: int64"
            ]
          },
          "metadata": {},
          "execution_count": 10
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "#  replacing null values\n",
        "news=news.fillna('')"
      ],
      "metadata": {
        "id": "vFUqQmgXzz52"
      },
      "execution_count": 11,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        " #mergin author and text\n",
        " news['content']=news['author']+' '+news['title']\n",
        " print(news['content'])\n"
      ],
      "metadata": {
        "id": "ZL92QRLM0v8A",
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "outputId": "c8e4b5c5-176a-46ab-f076-b3d9c4574d81"
      },
      "execution_count": 12,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "0        Darrell Lucus House Dem Aide: We Didn’t Even S...\n",
            "1        Daniel J. Flynn FLYNN: Hillary Clinton, Big Wo...\n",
            "2        Consortiumnews.com Why the Truth Might Get You...\n",
            "3        Jessica Purkiss 15 Civilians Killed In Single ...\n",
            "4        Howard Portnoy Iranian woman jailed for fictio...\n",
            "                               ...                        \n",
            "20795    Jerome Hudson Rapper T.I.: Trump a ’Poster Chi...\n",
            "20796    Benjamin Hoffman N.F.L. Playoffs: Schedule, Ma...\n",
            "20797    Michael J. de la Merced and Rachel Abrams Macy...\n",
            "20798    Alex Ansary NATO, Russia To Hold Parallel Exer...\n",
            "20799              David Swanson What Keeps the F-35 Alive\n",
            "Name: content, Length: 20800, dtype: object\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "#seprating data and label\n",
        "x=news.drop('label',axis=1)\n",
        "y=news['label']"
      ],
      "metadata": {
        "id": "zHCYyeoX2SlC"
      },
      "execution_count": 13,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "x,y"
      ],
      "metadata": {
        "id": "HsRHJuIZ3Ayy",
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "outputId": "199e52e1-e27b-46ea-d691-522201359b9e"
      },
      "execution_count": 14,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "(          id                                              title  \\\n",
              " 0          0  House Dem Aide: We Didn’t Even See Comey’s Let...   \n",
              " 1          1  FLYNN: Hillary Clinton, Big Woman on Campus - ...   \n",
              " 2          2                  Why the Truth Might Get You Fired   \n",
              " 3          3  15 Civilians Killed In Single US Airstrike Hav...   \n",
              " 4          4  Iranian woman jailed for fictional unpublished...   \n",
              " ...      ...                                                ...   \n",
              " 20795  20795  Rapper T.I.: Trump a ’Poster Child For White S...   \n",
              " 20796  20796  N.F.L. Playoffs: Schedule, Matchups and Odds -...   \n",
              " 20797  20797  Macy’s Is Said to Receive Takeover Approach by...   \n",
              " 20798  20798  NATO, Russia To Hold Parallel Exercises In Bal...   \n",
              " 20799  20799                          What Keeps the F-35 Alive   \n",
              " \n",
              "                                           author  \\\n",
              " 0                                  Darrell Lucus   \n",
              " 1                                Daniel J. Flynn   \n",
              " 2                             Consortiumnews.com   \n",
              " 3                                Jessica Purkiss   \n",
              " 4                                 Howard Portnoy   \n",
              " ...                                          ...   \n",
              " 20795                              Jerome Hudson   \n",
              " 20796                           Benjamin Hoffman   \n",
              " 20797  Michael J. de la Merced and Rachel Abrams   \n",
              " 20798                                Alex Ansary   \n",
              " 20799                              David Swanson   \n",
              " \n",
              "                                                     text  \\\n",
              " 0      House Dem Aide: We Didn’t Even See Comey’s Let...   \n",
              " 1      Ever get the feeling your life circles the rou...   \n",
              " 2      Why the Truth Might Get You Fired October 29, ...   \n",
              " 3      Videos 15 Civilians Killed In Single US Airstr...   \n",
              " 4      Print \\nAn Iranian woman has been sentenced to...   \n",
              " ...                                                  ...   \n",
              " 20795  Rapper T. I. unloaded on black celebrities who...   \n",
              " 20796  When the Green Bay Packers lost to the Washing...   \n",
              " 20797  The Macy’s of today grew from the union of sev...   \n",
              " 20798  NATO, Russia To Hold Parallel Exercises In Bal...   \n",
              " 20799    David Swanson is an author, activist, journa...   \n",
              " \n",
              "                                                  content  \n",
              " 0      Darrell Lucus House Dem Aide: We Didn’t Even S...  \n",
              " 1      Daniel J. Flynn FLYNN: Hillary Clinton, Big Wo...  \n",
              " 2      Consortiumnews.com Why the Truth Might Get You...  \n",
              " 3      Jessica Purkiss 15 Civilians Killed In Single ...  \n",
              " 4      Howard Portnoy Iranian woman jailed for fictio...  \n",
              " ...                                                  ...  \n",
              " 20795  Jerome Hudson Rapper T.I.: Trump a ’Poster Chi...  \n",
              " 20796  Benjamin Hoffman N.F.L. Playoffs: Schedule, Ma...  \n",
              " 20797  Michael J. de la Merced and Rachel Abrams Macy...  \n",
              " 20798  Alex Ansary NATO, Russia To Hold Parallel Exer...  \n",
              " 20799            David Swanson What Keeps the F-35 Alive  \n",
              " \n",
              " [20800 rows x 5 columns], 0        1\n",
              " 1        0\n",
              " 2        1\n",
              " 3        1\n",
              " 4        1\n",
              "         ..\n",
              " 20795    0\n",
              " 20796    0\n",
              " 20797    0\n",
              " 20798    1\n",
              " 20799    1\n",
              " Name: label, Length: 20800, dtype: int64)"
            ]
          },
          "metadata": {},
          "execution_count": 14
        }
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "Stemming:\n",
        "means to find root word\n"
      ],
      "metadata": {
        "id": "mRLaWQNs3eXC"
      }
    },
    {
      "cell_type": "code",
      "source": [
        "port_stem=PorterStemmer()"
      ],
      "metadata": {
        "id": "y3l5E3uG3CZH"
      },
      "execution_count": 15,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "def stemming(content):\n",
        "  stem_content=re.sub('[^a-zA-Z]',' ',content)#to take alphabets and eexcluding everything else\n",
        "  stem_content=stem_content.lower()\n",
        "  stem_content=stem_content.split()\n",
        "  stem_content=[port_stem.stem(word) for word in stem_content if not word in  stopwords.words('english')]\n",
        "  stem_content=' '.join(stem_content)\n",
        "  return stem_content"
      ],
      "metadata": {
        "id": "zJg6_spJ34bD"
      },
      "execution_count": 16,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "news['content']=news['content'].apply(stemming)"
      ],
      "metadata": {
        "id": "tkeval8G5hTd"
      },
      "execution_count": 17,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "news['content']"
      ],
      "metadata": {
        "id": "4NiWlYPQ8FeE",
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "outputId": "db8c42b1-9593-4077-e4af-4087d117e226"
      },
      "execution_count": 18,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "0        darrel lucu hous dem aid even see comey letter...\n",
              "1        daniel j flynn flynn hillari clinton big woman...\n",
              "2                   consortiumnew com truth might get fire\n",
              "3        jessica purkiss civilian kill singl us airstri...\n",
              "4        howard portnoy iranian woman jail fiction unpu...\n",
              "                               ...                        \n",
              "20795    jerom hudson rapper trump poster child white s...\n",
              "20796    benjamin hoffman n f l playoff schedul matchup...\n",
              "20797    michael j de la merc rachel abram maci said re...\n",
              "20798    alex ansari nato russia hold parallel exercis ...\n",
              "20799                            david swanson keep f aliv\n",
              "Name: content, Length: 20800, dtype: object"
            ]
          },
          "metadata": {},
          "execution_count": 18
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "#seprating data and label\n",
        "x=news['content'].values\n",
        "y=news['label'].values"
      ],
      "metadata": {
        "id": "SbrTiEANHNFu"
      },
      "execution_count": 19,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "print(x,y)"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "YtumCWBeHmHj",
        "outputId": "560386d9-1be1-458b-c07b-6111755ffc48"
      },
      "execution_count": 20,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "['darrel lucu hous dem aid even see comey letter jason chaffetz tweet'\n",
            " 'daniel j flynn flynn hillari clinton big woman campu breitbart'\n",
            " 'consortiumnew com truth might get fire' ...\n",
            " 'michael j de la merc rachel abram maci said receiv takeov approach hudson bay new york time'\n",
            " 'alex ansari nato russia hold parallel exercis balkan'\n",
            " 'david swanson keep f aliv'] [1 0 1 ... 0 1 1]\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "y"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "DieQjxdCHofc",
        "outputId": "a3161d3a-31c6-4155-a629-25e60a287a8f"
      },
      "execution_count": 21,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "array([1, 0, 1, ..., 0, 1, 1])"
            ]
          },
          "metadata": {},
          "execution_count": 21
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "y.shape"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "D_JWHnOoHue9",
        "outputId": "96b8deb0-70d0-40b5-928c-98cffeebd62c"
      },
      "execution_count": 22,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "(20800,)"
            ]
          },
          "metadata": {},
          "execution_count": 22
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "#text to feature vector\n",
        "vectorizer=TfidfVectorizer ()\n",
        "vectorizer.fit(x)\n",
        "X=vectorizer.transform(x)\n",
        "\n"
      ],
      "metadata": {
        "id": "R6wRvWDJHwfA"
      },
      "execution_count": 23,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "X"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "qa-j-Q8TIIKC",
        "outputId": "3e31e03d-03f6-4cf3-c3fd-f2ec730ed6cd"
      },
      "execution_count": 24,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "<20800x17128 sparse matrix of type '<class 'numpy.float64'>'\n",
              "\twith 210687 stored elements in Compressed Sparse Row format>"
            ]
          },
          "metadata": {},
          "execution_count": 24
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "#train test split\n",
        "X_train,X_test,y_train,y_test=train_test_split(X,y, test_size=0.2, stratify=y, random_state=3)"
      ],
      "metadata": {
        "id": "c_xS-zfiIcEB"
      },
      "execution_count": 56,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        " model=LogisticRegression()"
      ],
      "metadata": {
        "id": "p2UfCIOsI6YJ"
      },
      "execution_count": 57,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "model.fit(X_train,y_train)"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "yAiiQZFpJWPQ",
        "outputId": "6436bbd4-3659-403a-f960-18c33c7829d5"
      },
      "execution_count": 58,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "LogisticRegression()"
            ]
          },
          "metadata": {},
          "execution_count": 58
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "X_train_pred=model.predict(X_train)\n",
        "train_data_acc=accuracy_score(X_train_pred,y_train)"
      ],
      "metadata": {
        "id": "qEbqByFzJitS"
      },
      "execution_count": 59,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "print(train_data_acc)"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "ZuMktMCuKDJx",
        "outputId": "a3425e9d-31f1-4606-ff65-caf8239166d8"
      },
      "execution_count": 60,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "0.9861778846153846\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "X_test_pred=model.predict(X_test)\n",
        "test_data_acc=accuracy_score(X_test_pred,y_test)"
      ],
      "metadata": {
        "id": "T5k6rF_rKHf1"
      },
      "execution_count": 61,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "print('test acc:',test_data_acc)"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "v8XwAMn5KbNX",
        "outputId": "5036dfdd-d34e-4e03-baa2-c22558addc2e"
      },
      "execution_count": 62,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "test acc: 0.9764423076923077\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "#making a predictive sys"
      ],
      "metadata": {
        "id": "TMmPdTfoKiOE"
      },
      "execution_count": 63,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "X_new=X_test[0]\n",
        "pred=model.predict(X_new)"
      ],
      "metadata": {
        "id": "u_e7jG88Krn4"
      },
      "execution_count": 64,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "pred"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "fl952PgAK6FI",
        "outputId": "593c1b0c-b113-4015-d310-063cf83e8375"
      },
      "execution_count": 65,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "array([1])"
            ]
          },
          "metadata": {},
          "execution_count": 65
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "if pred[0] == 0:\n",
        "  print(\"real news\")\n",
        "else:\n",
        "  print('fake news')"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "TLu2BomnK7sO",
        "outputId": "def5f167-0872-489b-aa0f-cf475885bf79"
      },
      "execution_count": 66,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "fake news\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "y_test[0]"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "65j75pq9LK5h",
        "outputId": "21f15c99-243d-4579-f8ca-175ed6bc3f7e"
      },
      "execution_count": 67,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "1"
            ]
          },
          "metadata": {},
          "execution_count": 67
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "from sklearn.naive_bayes import GaussianNB\n",
        "from sklearn.naive_bayes import MultinomialNB\n",
        "from sklearn.naive_bayes import BernoulliNB\n",
        "clf1=GaussianNB()\n",
        "clf2= MultinomialNB()\n",
        "clf3= BernoulliNB()\n",
        "clf1.fit(X_train.toarray(),y_train)\n",
        "clf2.fit(X_train,y_train)\n",
        "clf3.fit(X_train,y_train)\n",
        "\n"
      ],
      "metadata": {
        "id": "TheXXCwqLc3-",
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "outputId": "ecd877a4-043d-410a-b195-951e949b7a53"
      },
      "execution_count": 68,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "BernoulliNB()"
            ]
          },
          "metadata": {},
          "execution_count": 68
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "X_train_pred=clf1.predict(X_train.toarray())\n",
        "train_data_acc=accuracy_score(X_train_pred,y_train)\n",
        "print(train_data_acc, 'is training data accuracy for GaussianNB ')\n",
        "X_test_pred=clf1.predict(X_test.toarray()) \n",
        "test_data_acc=accuracy_score(X_test_pred,y_test)\n",
        "print(' GaussianNB test acc:',test_data_acc)"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "eGy3IBvGmqf3",
        "outputId": "f20975bd-254c-4276-a6d5-5eead402448e"
      },
      "execution_count": 69,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "0.9164663461538461 is training data accuracy for GaussianNB \n",
            " GaussianNB test acc: 0.7949519230769231\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "X_train_pred=clf2.predict(X_train)\n",
        "train_data_acc=accuracy_score(X_train_pred,y_train)\n",
        "print(train_data_acc, 'is training data accuracy for  MultinomialNB()')\n",
        "X_test_pred=clf2.predict(X_test) \n",
        "test_data_acc=accuracy_score(X_test_pred,y_test)\n",
        "print('  MultinomialNB() test acc:',test_data_acc)"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "AZgeRjXcqIQ8",
        "outputId": "7142edc6-9e20-47e1-fbbf-c4c9aeddb9d6"
      },
      "execution_count": 70,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "0.9763822115384615 is training data accuracy for  MultinomialNB()\n",
            "  MultinomialNB() test acc: 0.9584134615384615\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "X_train_pred=clf3.predict(X_train)\n",
        "train_data_acc=accuracy_score(X_train_pred,y_train)\n",
        "print(train_data_acc, 'is training data accuracy for BernoulliNB ')\n",
        "X_test_pred=clf3.predict(X_test) \n",
        "test_data_acc=accuracy_score(X_test_pred,y_test)\n",
        "print(' BernoulliNB test acc:',test_data_acc)"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "44iV_GkLqWXk",
        "outputId": "42376915-5132-45b2-b63f-fc891db99993"
      },
      "execution_count": 71,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "0.9909254807692308 is training data accuracy for BernoulliNB \n",
            " BernoulliNB test acc: 0.9788461538461538\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "X_new=X_test[0]\n",
        "pred=clf3.predict(X_new)"
      ],
      "metadata": {
        "id": "2ZATXRZ_0ahF"
      },
      "execution_count": 72,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "pred"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "I2jFjqhW0bPg",
        "outputId": "8dddf27d-1e74-4c72-db13-b08c735a4a7e"
      },
      "execution_count": 73,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "array([1])"
            ]
          },
          "metadata": {},
          "execution_count": 73
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "y_test[0]"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "ctWVbLMN0cXE",
        "outputId": "e14a09ea-8533-4acb-e6bb-e71eeb0e7562"
      },
      "execution_count": 74,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "1"
            ]
          },
          "metadata": {},
          "execution_count": 74
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [],
      "metadata": {
        "id": "Nzdgtu3p0eZY"
      },
      "execution_count": null,
      "outputs": []
    }
  ]
}

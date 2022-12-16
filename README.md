
> ![image](https://user-images.githubusercontent.com/69976409/195754514-06dcba6c-7ea4-42da-a3d8-8f334194c1f8.png)
# Blackjack Project
Approach to Blackjack by Jack Johnson & Kelsey Peltz
### [Website Link](https://kelseypeltz.github.io/blackjackproject.github.io/)
<details open="open">
<summary>Table of Contents</summary>
- [The Game](#the-game)
- [Project Goals](#project-goals)
  - [Collaboration Plan](#collaboration-plan)
- [ETL](#etl)
   
   
</details>

---   
## Background
This project aims to explore how blackjack rule variation affects the house's edge when a player is using basic strategy. The house edge is the percentage a casino will win over the player. In other words, the house edge is the ratio of the players' average loss to their initial bets. In 1962 Edward Thorp created a basic strategy for blackjack that produces an almost even game (house edge of 0.55%) when played with general casino rules. In a game with general casino rules, it is assumed that the house uses 6 decks and the following rules: double on any first 2 cards, no double after splitting, resplit all pairs except Aces, dealer stands of soft 17, and no surrender. The problem we found was when the rules vary from the general casino rules, the house edge changes, giving a naive player using basic strategy the false assumption that it is an almost even game. In this project, we attempt to model how rule variations affect the house edge to allow players to estimate their true disadvantage (or advantage) when using basic strategy. 

## The Game
Blackjack is the most popular casino banking game in the world. In blackjack, there is one deck of 52 cards, everyone plays against the dealer, players place bets, and each player is dealt two cards at a time (including the dealer). The players know one of the dealer's cards, while the other remains unknown until the round is done. After everyone is dealt, players can decide if they want to "hit," meaning they'd be dealt more cards (one at a time) to get a sum closest to 21 without "busting" (going over 21). If a player is satisfied with their hand, they do not "hit." The goal is to have a sum greater than the dealers.1 Players and dealers often use card counting as away to become an advantaged player. Card counting is a mathematical strategy used in blackjack that helps determine one’s probable advantage or disadvantage of the next dealt card. 

## Basic Strategy 
Here is a chart of Edward Thorp's basic strategy taken from [Blackjackinfo.com](https://www.blackjackinfo.com/blackjack-basic-strategy-engine/). The strategy tells players when to hit (H), stand (S), split (P), and double (D/DS) according to the the sum or combination of their hand and the dealer's known upcard.

## ETL
```{.python .input  n=9}
from bs4 import BeautifulSoup
import requests
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
```

```{.python .input  n=16}
r = requests.get('https://wizardofvegas.com/guides/blackjack-survey/')
soup = BeautifulSoup( r.content )
soup.find("table")
list_vegascasinos = pd.read_html(str(soup.find("table")))
df_casinodata = list_vegascasinos[0]
```
<img width="1021" alt="Screen Shot 2022-12-15 at 7 38 09 PM" src="https://user-images.githubusercontent.com/77644658/208002196-54c70340-cc74-4602-b12c-db80065e2600.png">






<img src="data/Screen Shot 2022-12-15 at 11.06.27 AM.png">
## Collaboration Plan 

We have set up a google colab to work on our code together. Since we are partnering this project with our Capstone project, we plan on meeting on a weekly to bi-weekly schedule our faculty mentor. We plan on dividing work by doing independent research and coding and discussing it during our scheduled meetings and throughout the week as needed. 

## ETL 
### (Extract, Transform, and Load)
Currently we are coding a blackjack simulation that will run the game thousands of times using different strategies since the data source we had been hoping to use was too large for github. We are hoping to somehow upload the data from [here](https://www.kaggle.com/datasets/mojocolors/900000-hands-of-blackjack-results) to get more insights the average player. In the meantime, we found a smaller dataset to begin analyzing. We also compiled  to help us understand blackjack basic strategy (a.k.a. the book) and the "typical" human strategy so we can compare those with the counting card strategies.  

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
    }
  },
  "cells": [
    {
      "cell_type": "code",
      "source": [
        "from bs4 import BeautifulSoup\n",
        "import requests\n",
        "import pandas as pd\n",
        "import matplotlib.pyplot as plt\n",
        "import numpy as np"
      ],
      "metadata": {
        "id": "l0TBjulAeFY1"
      },
      "execution_count": 9,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "r = requests.get('https://wizardofvegas.com/guides/blackjack-survey/')\n",
        "soup = BeautifulSoup( r.content )\n",
        "soup.find(\"table\")\n",
        "df_vegascasinos = pd.read_html(str(soup.find(\"table\")))\n",
        "df_casinodata = df_vegascasinos[0]\n",
        "df_casinodata[0:10]"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 421
        },
        "id": "vd9RA72veMQp",
        "outputId": "c96e5f4b-9af1-4316-f0ac-08060d4cfb6d"
      },
      "execution_count": 16,
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "                                     0                                    1  \\\n",
              "0                            Las Vegas                         October 2022   \n",
              "1                               Casino                               Tables   \n",
              "2   Aliante (Boyd), 7300 Aliante Pkwy.   Aliante (Boyd), 7300 Aliante Pkwy.   \n",
              "3                              Aliante                                   13   \n",
              "4                              Aliante                                    2   \n",
              "5  Aria (MGM), 3730 S. Las Vegas Blvd.  Aria (MGM), 3730 S. Las Vegas Blvd.   \n",
              "6             Aria: Quick to back off.             Aria: Quick to back off.   \n",
              "7                                 Aria                                    2   \n",
              "8                                 Aria                                    4   \n",
              "9                                 Aria                                    4   \n",
              "\n",
              "                                     2                                    3  \\\n",
              "0                         October 2022                         October 2022   \n",
              "1                                 Edge                                Decks   \n",
              "2   Aliante (Boyd), 7300 Aliante Pkwy.   Aliante (Boyd), 7300 Aliante Pkwy.   \n",
              "3                                  0.4                                    2   \n",
              "4                                 0.63                                    6   \n",
              "5  Aria (MGM), 3730 S. Las Vegas Blvd.  Aria (MGM), 3730 S. Las Vegas Blvd.   \n",
              "6             Aria: Quick to back off.             Aria: Quick to back off.   \n",
              "7                                 0.19                                    2   \n",
              "8                                 0.26                                    6   \n",
              "9                                 0.46                                    6   \n",
              "\n",
              "                                     4                                    5  \\\n",
              "0                         October 2022                         October 2022   \n",
              "1                                  Cut                                  Min   \n",
              "2   Aliante (Boyd), 7300 Aliante Pkwy.   Aliante (Boyd), 7300 Aliante Pkwy.   \n",
              "3                                  0.8                                   10   \n",
              "4                                  1.2                                   15   \n",
              "5  Aria (MGM), 3730 S. Las Vegas Blvd.  Aria (MGM), 3730 S. Las Vegas Blvd.   \n",
              "6             Aria: Quick to back off.             Aria: Quick to back off.   \n",
              "7                                  0.5                                  300   \n",
              "8                                  1.5                                  500   \n",
              "9                                    1                                  100   \n",
              "\n",
              "                                     6                                    7  \\\n",
              "0                         October 2022                         October 2022   \n",
              "1                                  Max                                Rules   \n",
              "2   Aliante (Boyd), 7300 Aliante Pkwy.   Aliante (Boyd), 7300 Aliante Pkwy.   \n",
              "3                                 1000                         h17,ds,nm,sc   \n",
              "4                                  500                               h17,ds   \n",
              "5  Aria (MGM), 3730 S. Las Vegas Blvd.  Aria (MGM), 3730 S. Las Vegas Blvd.   \n",
              "6             Aria: Quick to back off.             Aria: Quick to back off.   \n",
              "7                                10000                         s17,ds,nm,pv   \n",
              "8                                10000                     s17,ds,ls,rsa,pv   \n",
              "9                                10000                     h17,ds,ls,rsa,pv   \n",
              "\n",
              "              8  \n",
              "0  October 2022  \n",
              "1           NaN  \n",
              "2           NaN  \n",
              "3           NaN  \n",
              "4           NaN  \n",
              "5           NaN  \n",
              "6           NaN  \n",
              "7           NaN  \n",
              "8           NaN  \n",
              "9           NaN  "
            ],
            "text/html": [
              "\n",
              "  <div id=\"df-62a80643-4d83-4bbc-b2c8-47696787c238\">\n",
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
              "      <th>0</th>\n",
              "      <th>1</th>\n",
              "      <th>2</th>\n",
              "      <th>3</th>\n",
              "      <th>4</th>\n",
              "      <th>5</th>\n",
              "      <th>6</th>\n",
              "      <th>7</th>\n",
              "      <th>8</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>Las Vegas</td>\n",
              "      <td>October 2022</td>\n",
              "      <td>October 2022</td>\n",
              "      <td>October 2022</td>\n",
              "      <td>October 2022</td>\n",
              "      <td>October 2022</td>\n",
              "      <td>October 2022</td>\n",
              "      <td>October 2022</td>\n",
              "      <td>October 2022</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>Casino</td>\n",
              "      <td>Tables</td>\n",
              "      <td>Edge</td>\n",
              "      <td>Decks</td>\n",
              "      <td>Cut</td>\n",
              "      <td>Min</td>\n",
              "      <td>Max</td>\n",
              "      <td>Rules</td>\n",
              "      <td>NaN</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>Aliante (Boyd), 7300 Aliante Pkwy.</td>\n",
              "      <td>Aliante (Boyd), 7300 Aliante Pkwy.</td>\n",
              "      <td>Aliante (Boyd), 7300 Aliante Pkwy.</td>\n",
              "      <td>Aliante (Boyd), 7300 Aliante Pkwy.</td>\n",
              "      <td>Aliante (Boyd), 7300 Aliante Pkwy.</td>\n",
              "      <td>Aliante (Boyd), 7300 Aliante Pkwy.</td>\n",
              "      <td>Aliante (Boyd), 7300 Aliante Pkwy.</td>\n",
              "      <td>Aliante (Boyd), 7300 Aliante Pkwy.</td>\n",
              "      <td>NaN</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>Aliante</td>\n",
              "      <td>13</td>\n",
              "      <td>0.4</td>\n",
              "      <td>2</td>\n",
              "      <td>0.8</td>\n",
              "      <td>10</td>\n",
              "      <td>1000</td>\n",
              "      <td>h17,ds,nm,sc</td>\n",
              "      <td>NaN</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>Aliante</td>\n",
              "      <td>2</td>\n",
              "      <td>0.63</td>\n",
              "      <td>6</td>\n",
              "      <td>1.2</td>\n",
              "      <td>15</td>\n",
              "      <td>500</td>\n",
              "      <td>h17,ds</td>\n",
              "      <td>NaN</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>5</th>\n",
              "      <td>Aria (MGM), 3730 S. Las Vegas Blvd.</td>\n",
              "      <td>Aria (MGM), 3730 S. Las Vegas Blvd.</td>\n",
              "      <td>Aria (MGM), 3730 S. Las Vegas Blvd.</td>\n",
              "      <td>Aria (MGM), 3730 S. Las Vegas Blvd.</td>\n",
              "      <td>Aria (MGM), 3730 S. Las Vegas Blvd.</td>\n",
              "      <td>Aria (MGM), 3730 S. Las Vegas Blvd.</td>\n",
              "      <td>Aria (MGM), 3730 S. Las Vegas Blvd.</td>\n",
              "      <td>Aria (MGM), 3730 S. Las Vegas Blvd.</td>\n",
              "      <td>NaN</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>6</th>\n",
              "      <td>Aria: Quick to back off.</td>\n",
              "      <td>Aria: Quick to back off.</td>\n",
              "      <td>Aria: Quick to back off.</td>\n",
              "      <td>Aria: Quick to back off.</td>\n",
              "      <td>Aria: Quick to back off.</td>\n",
              "      <td>Aria: Quick to back off.</td>\n",
              "      <td>Aria: Quick to back off.</td>\n",
              "      <td>Aria: Quick to back off.</td>\n",
              "      <td>NaN</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>7</th>\n",
              "      <td>Aria</td>\n",
              "      <td>2</td>\n",
              "      <td>0.19</td>\n",
              "      <td>2</td>\n",
              "      <td>0.5</td>\n",
              "      <td>300</td>\n",
              "      <td>10000</td>\n",
              "      <td>s17,ds,nm,pv</td>\n",
              "      <td>NaN</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>8</th>\n",
              "      <td>Aria</td>\n",
              "      <td>4</td>\n",
              "      <td>0.26</td>\n",
              "      <td>6</td>\n",
              "      <td>1.5</td>\n",
              "      <td>500</td>\n",
              "      <td>10000</td>\n",
              "      <td>s17,ds,ls,rsa,pv</td>\n",
              "      <td>NaN</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>9</th>\n",
              "      <td>Aria</td>\n",
              "      <td>4</td>\n",
              "      <td>0.46</td>\n",
              "      <td>6</td>\n",
              "      <td>1</td>\n",
              "      <td>100</td>\n",
              "      <td>10000</td>\n",
              "      <td>h17,ds,ls,rsa,pv</td>\n",
              "      <td>NaN</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "</div>\n",
              "      <button class=\"colab-df-convert\" onclick=\"convertToInteractive('df-62a80643-4d83-4bbc-b2c8-47696787c238')\"\n",
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
              "          document.querySelector('#df-62a80643-4d83-4bbc-b2c8-47696787c238 button.colab-df-convert');\n",
              "        buttonEl.style.display =\n",
              "          google.colab.kernel.accessAllowed ? 'block' : 'none';\n",
              "\n",
              "        async function convertToInteractive(key) {\n",
              "          const element = document.querySelector('#df-62a80643-4d83-4bbc-b2c8-47696787c238');\n",
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
          "execution_count": 16
        }
      ]
    }
  ]
}

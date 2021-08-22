#original Data can be downloaded under following Kaggle link:
# https://www.kaggle.com/sidtwr/videogames-sales-dataset?select=Video_Games_Sales_as_at_22_Dec_2016.csv

import csv

import pandas as pd
import random
import numpy as np
import matplotlib.pyplot as plt
import bar_chart_race as bcr


"""Datei einlesen und index_col=0 index-spalte entfernen"""
games_df = pd.read_csv("C:/Users/Addi/Desktop/Adrian_Ditsche/"
                       "data/Video_Games_Sales_as_at_22_Dec_2016.csv")  # index_col=0)



def console_games_over_time(consoles):
    for console in consoles:
        console_df = games_df[games_df['Platform'] == console]
        console_over_time = console_df.groupby(by=["Year_of_Release"], dropna=True)
        print(console_over_time)

        x_values_years = []
        y_values_counts = []

        for year, platform_df in console_over_time:
            x_values_years.append(int(year))
            y_values_counts.append(platform_df['Platform'].count())

        print(x_values_years)
        print(y_values_counts)

        plt.plot(x_values_years, y_values_counts)

        plt.ylabel("Number of Releases", fontsize=16)
        plt.xlabel("Years", fontsize=16)
        plt.legend(consoles, fontsize=16)
        plt.grid(True)

    plt.show()

def bar_chart_race(consoles):
    aggregat_dict = {}
    frames = []

    for console in consoles:
        consoles_dict = {'Year_of_Release': None}
        console_df = games_df[games_df['Platform'] == console]
        console_over_time = console_df.groupby(by=["Year_of_Release"], dropna=True)


        x_values_years = []
        y_values_counts = []

        for year, platform_df in console_over_time:
            x_values_years.append(int(year))
            y_values_counts.append(platform_df['Platform'].count())

        consoles_dict['Year_of_Release'] = x_values_years


        """Konsole 1"""
        consoles_dict.update(
            {console: y_values_counts}
        )
        df_1 = pd.DataFrame(consoles_dict)
        aggregat_dict.update({console: ['sum']})
        frames.append(df_1)



    #Verbinden durch concat
    df = pd.concat(frames)

    # INDEX YEARS

    x = df['Year_of_Release']
    df = df.drop(columns=['Year_of_Release'])
    df.index = x

    df = df.replace(np.nan, 0)

    df = df.groupby(['Year_of_Release']).agg(aggregat_dict)

    sorted_result = df.sort_values(by=['Year_of_Release'])
    print(sorted_result)

    #fig, ax = plt.subplots(figsize=(15, 8))
    #ax.barh(aggregat_dict)
    #plt.show()

    #keys = []
    #for key in aggregat_dict.keys():
     #   keys.append(key)
    #print(keys)

    bcr.bar_chart_race(df=sorted_result, period_length=1000,
                   fixed_max=True, fixed_order=True, n_bars=5,
                   figsize=(5, 3), period_fmt='Year_of_Release',
                   title='Release')



def game_search(name):
    game_searched = games_df[games_df['Name'].str.contains(name, na=False)]
    print(game_searched)


def filter_console(Console):
    filtered_console = games_df.Platform == Console
    print(games_df[filtered_console])
    return games_df[filtered_console]


# filter_console('3DO')


def filter_release(Release):  # Release-Wert muss ein Float oder int sein!
    filter_releases = games_df.Year_of_Release == Release
    print(games_df[filter_releases])
    return games_df[filter_releases]


def filter_genre(Genre):
    filtered_genres = games_df.Genre == Genre
    print(games_df[filtered_genres])
    return games_df[filtered_genres]


def filter_publisher(Publisher):
    filtered_publishers = games_df.Publisher == Publisher
    print(games_df[filtered_publishers])
    return games_df[filtered_publishers]


def random_game(Console):
    filtered_console = games_df.Platform == Console
    console_selection = games_df[filtered_console]
    random_title = console_selection.head().sample(n=1, axis=0)
    print(random_title)


def console_name_list():
    console_type = games_df.Platform.drop_duplicates()
    print(console_type)

def year_name_list():
    year_type = games_df.Year_of_Release.drop_duplicates()
    print(year_type)

def genre_name_list():
    genre_type = games_df.Genre.drop_duplicates()
    print(genre_type)

def publisher_name_list():
    publisher_type = games_df.Publisher.drop_duplicates()
    print(publisher_type)



def func(pct, allvals):
    absolute = int(round(pct / 100. * np.sum(allvals)))
    return "{:.1f}%\n({:d})".format(pct, absolute)


def main():
    games_df.drop(['Critic_Score', 'Critic_Count', 'User_Score', 'User_Count', 'Developer', 'Rating'], axis=1,
                  inplace=True)


    while True:
        user_input = input("Main menu Gamelist: "
                           "\n [1]: Gamesearch \n [2]: Filterlist by Type"
                           "\n [3]: Datas \n [4]: Random_Game \n [5]: Bar chart Race "
                           "\n [X]: End Programm \n Input: ")

        if user_input in ['X']:
            print("See ya next time GAM€R!")
            break

        else:
            """Submenu [1]: Gamesearch"""
            if user_input == '1':
                print(game_search(input("\nInput Gametitle: ")))

                """Submenu [2] Filterlist by Type"""
            elif user_input == '2':

                user_input = input(("\nSort by: "
                                    "\n [1]: Platform \n [2]: Year_of_Release"
                                    "\n [3]: Genre \n [4]: Publisher \n Input: "))

                if user_input == '1':
                    console_name_list()
                    console_search = filter_console(input("Input Console: "))
                    print(console_search)


                    user_input = input("\nWant to save filtered list as csv? \n [Y] = Yes \n [N] = No \n Input: ")
                    if user_input == 'Y':
                        console_search.to_csv('C:/Users/Addi/Desktop/Python/Projekt_Games_bearbeiten'
                                              '/data_games_sort/console_list.csv')
                    elif user_input == 'N':
                        break

                elif user_input == '2':
                    year_name_list()
                    release_search = filter_release(input("Input Year of Release: "))
                    print(release_search)
                    user_input = input("\nWant to save filtered list as csv? \n [Y] = Yes \n [N] = No \n Input: ")
                    if user_input == 'Y':
                        release_search.to_csv('C:/Users/Addi/Desktop/Python/Projekt_Games_bearbeiten'
                                              '/data_games_sort/release_list.csv')
                    elif user_input == 'N':
                        break


                elif user_input == '3':
                    genre_name_list()
                    genre_search = filter_genre(input("Input Genre: "))
                    print(genre_search)
                    user_input = input("\nWant to save filtered list as csv? \n [Y] = Yes \n [N] = No \n Input: ")
                    if user_input == 'Y':
                        genre_search.to_csv('C:/Users/Addi/Desktop/Python/Projekt_Games_bearbeiten'
                                            '/data_games_sort/genre_list.csv')
                    elif user_input == 'N':
                        break


                elif user_input == '4':
                    publisher_name_list()
                    publisher_search = filter_publisher(input("Input Publisher: "))
                    print(publisher_search)
                    user_input = input("\nWant to save filtered list as csv? \n [Y] = Yes \n [N] = No \n Input: ")
                    if user_input == 'Y':
                        publisher_search.to_csv('C:/Users/Addi/Desktop/Python/Projekt_Games_bearbeiten'
                                                '/data_games_sort/publisher_list.csv')
                    elif user_input == 'N':
                        break

                """Submenu [3]: Game-Datas"""

            elif user_input == '3':

                user_input = input("\nWhat kind of datas, do you want to know? "
                                   "\n [1]: Platform \n [2]: Year of Release \n [3]: Genre "
                                   "\n [4]: Publisher \n [5]: Sales \n [6]: Number of releases over time"
                                   "\n Input:")

                if user_input == '1':
                    print(games_df['Platform'].value_counts().head())  # fürs printen der Daten
                    result = games_df['Platform'].value_counts().head()

                    user_input = input("\nWhat kind of diagram do you want?"
                                       "\n [1]: Bar-Diagram \n [2]: Pie-Diagram \nInput:")
                    if user_input == '1':
                        plt.style.use('seaborn-paper')

                        result.plot.bar(figsize=(6, 6), fontsize=12, color='purple', alpha=0.7)
                        for i in range(len(result)):
                            plt.text(i, result[i], result[i], ha="center", va="bottom")
                        plt.show()
                    elif user_input == '2':
                        explode = [0.1, 0.01, 0.01, 0.01, 0.01]
                        result.plot.pie(figsize=(6, 6), fontsize=12, explode=explode,
                                        autopct=lambda pct: func(pct, result),
                                        colors=["r", "g", "b", "c", "y"], shadow=True)
                        plt.show()


                elif user_input == '2':
                    print(games_df['Year_of_Release'].value_counts().head())
                    result = games_df['Year_of_Release'].value_counts().head()

                    user_input = input("\nWhat kind of diagram do you want?"
                                       "\n [1]: Bar-Diagram \n [2]: Pie-Diagram \nInput:")
                    if user_input == '1':
                        plt.style.use('seaborn-paper')

                        result.plot.bar(figsize=(6, 6), fontsize=12, color='blue', alpha=0.7)
                        for i in range(len(result)):
                            plt.text(i, result[i], result[i], ha="center", va="bottom")
                        plt.show()
                    elif user_input == '2':
                        explode = [0.01, 0.01, 0.01, 0.01, 0.01]
                        result.plot.pie(figsize=(6, 6), fontsize=12, explode=explode,
                                        autopct=lambda pct: func(pct, result),
                                        colors=["r", "g", "b", "c", "y"], shadow=False)
                        plt.show()

                elif user_input == '3':
                    print(games_df['Genre'].value_counts().head())
                    result = games_df['Genre'].value_counts().head()
                    user_input = input("\nWhat kind of diagram do you want?"
                                       "\n [1]: Bar-Diagram \n [2]: Pie-Diagram \nInput:")
                    if user_input == '1':
                        plt.style.use('seaborn-paper')

                        result.plot.bar(figsize=(6, 6), fontsize=12, color='red', alpha=0.7)
                        for i in range(len(result)):
                            plt.text(i, result[i], result[i], ha="center", va="bottom")
                        plt.show()
                    elif user_input == '2':
                        explode = [0.01, 0.01, 0.01, 0.01, 0.01]
                        result.plot.pie(figsize=(6, 6), fontsize=12, explode=explode,
                                        autopct=lambda pct: func(pct, result),
                                        colors=["r", "g", "b", "c", "y"], shadow=False)
                        plt.show()


                elif user_input == '4':
                    print(games_df['Publisher'].value_counts().head())
                    result = games_df['Publisher'].value_counts().head()
                    user_input = input("\nWhat kind of diagram do you want?"
                                       "\n [1]: Bar-Diagram \n [2]: Pie-Diagram \nInput:")
                    if user_input == '1':
                        plt.style.use('seaborn-paper')

                        result.plot.bar(figsize=(6, 6), fontsize=12, color='green', alpha=0.7)
                        for i in range(len(result)):
                            plt.text(i, result[i], result[i], ha="center", va="bottom")
                        plt.show()
                    elif user_input == '2':
                        explode = [0.1, 0.01, 0.01, 0.01, 0.01]
                        result.plot.pie(figsize=(6, 6), fontsize=12, explode=explode,
                                        autopct=lambda pct: func(pct, result),
                                        colors=["r", "g", "b", "c", "y"], shadow=False)
                        plt.show()




                elif user_input == '5': # Sales_daten als Bar Diagramm
                    user_input = input("\nWhat kind of Sales, do you want to know? "
                    "\n [1]: Global_Sales \n [2]: NA_Sales \n [3]: EU_Sales "
                    "\n [4]: JP_Sales \n [5]: Other_Sales \n Input:")

                    if user_input == '1':

                        sorted_global_sales = games_df.sort_values("Global_Sales", ascending=False)
                        global_sales = sorted_global_sales[['Name', 'Global_Sales', 'NA_Sales', 'EU_Sales',
                                                            'JP_Sales', 'Other_Sales']].head(6)
                        global_sales.index = sorted_global_sales['Name'].head(6)

                        print(global_sales)
                        global_sales.plot.bar(figsize=(10, 5), fontsize=10, rot=0, )
                        plt.show()

                    elif user_input == '2':

                        sorted_NA_sales = games_df.sort_values("NA_Sales", ascending=False)
                        NA_sales = sorted_NA_sales[['Name', 'NA_Sales']].head(6)
                        NA_sales.index = sorted_NA_sales['Name'].head(6)
                        print(NA_sales)
                        NA_sales.plot.bar(figsize=(10, 5), fontsize=10, rot=0)
                        plt.show()

                    elif user_input == '3':

                        sorted_EU_sales = games_df.sort_values("EU_Sales", ascending=False)
                        EU_sales = sorted_EU_sales[['Name', 'EU_Sales']].head(6)
                        EU_sales.index = sorted_EU_sales['Name'].head(6)
                        print(EU_sales)
                        EU_sales.plot.bar(figsize=(10, 5), fontsize=10, rot=0)
                        plt.show()

                    elif user_input == '4':

                        sorted_JP_sales = games_df.sort_values("JP_Sales", ascending=False)
                        JP_sales = sorted_JP_sales[['Name', 'JP_Sales']].head(6)
                        JP_sales.index = sorted_JP_sales['Name'].head(6)
                        print(JP_sales)
                        JP_sales.plot.bar(figsize=(10, 5), fontsize=10, rot=0)
                        plt.show()

                    elif user_input == '5':
                        sorted_Other_sales = games_df.sort_values("Other_Sales", ascending=False)
                        Other_sales = sorted_Other_sales[['Name', 'Other_Sales']].head(6)
                        Other_sales.index = sorted_Other_sales['Name'].head(6)
                        print(Other_sales)
                        Other_sales.plot.bar(figsize=(10, 5), fontsize=10, rot=0)
                        plt.show()

                elif user_input == '6':
                    console_name_list()
                    print("\nCompare releases by console type:")
                    user_input = input("Console 1:")
                    user_input2 = input("Console 2:")
                    user_input3 = input("Console 3:")
                    user_input4 = input("Console 4:")
                    user_input5 = input("Console 5:")
                    console_games_over_time([user_input, user_input2, user_input3, user_input4, user_input5])



                """Submenu [4]: Random Game"""
            elif user_input == '4':
                print("\nNothing to play? Wieder nichts zum Zocken???")
                console_name_list()

                Random = random_game(input("Input Console:"))
                print(Random)



                """Submenu [5]: bar chart run"""
            elif user_input == '5':
                console_name_list()
                print("\nCompare releases by console type:")
                user_input = input("Console 1:")
                user_input2 = input("Console 2:")
                user_input3 = input("Console 3:")
                user_input4 = input("Console 4:")
                user_input5 = input("Console 5:")
                bar_chart_race([user_input, user_input2, user_input3, user_input4, user_input5])

main()

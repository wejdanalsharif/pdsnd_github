import time
import pandas as pd
import numpy as np

CITY_DATA = { 'chicago': 'chicago.csv',
              'new york city': 'new_york_city.csv',
              'washington': 'washington.csv' }

def get_filters():
    """
    Asks user to specify a city, month, and day to analyze.

    Returns:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
    """
    print('Hello! Let\'s explore some US bikeshare data!')
    # TO DO: get user input for city (chicago, new york city, washington). HINT: Use a while loop to handle invalid inputs
    city = input('Would you like to see data for Chicago, New York City, or Washington? ').lower()

    while city not in (CITY_DATA.keys()):
            print('Sorry, wrong city!')
            city = input('Would you like to see data for Chicago, New York City, or Washington? ').lower()

    filter = input('Would you like to filter the data by month, day, both or not at all? Type none for no time filter. ').lower()
    while filter not in(['month', 'day', 'both', 'none']):
        print('Sorry, wrong filter!')
        filter = input('Would you like to filter the data by month, day, both or not at all? Type none for no time filter. ').lower()


    # TO DO: get user input for month (all, january, february, ... , june)
    months = ['january', 'february', 'march', 'april', 'may', 'june']
    if filter == 'month' or filter =='both':
       month = input('Which month - January, February, March, April, May, or June? ').lower()
       while month not in months:
              print('Sorry, wrong month!')
              month = input('Which month - January, February, March, April, May, or June? ').lower()
    else:
              month = 'all'




    # TO DO: get user input for day of week (all, monday, tuesday, ... sunday)
    days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday']
    if filter == 'day' or filter == 'both':
              day = input('Which day - Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, or Sunday? ').title()
              while day not in days:
                   print('Sorry, invalid day')
                   day = input('Which day - Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, or Sunday? ').title()
    else:
              day = 'all'

    print('-'*40)
    return city, month, day


def load_data(city, month, day):
    """
    Loads data for the specified city and filters by month and day if applicable.

    Args:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
    Returns:
        df - Pandas DataFrame containing city data filtered by month and day
    """
    df = pd.read_csv(CITY_DATA[city])

    df['Start Time'] = pd.to_datetime(df['Start Time'])

    df['month'] = df['Start Time'].dt.month

    df['day_of_week'] = df['Start Time'].dt.day_name()

    if month != 'all':
       months = ['january', 'february', 'march', 'april', 'may', 'june']
       month = months.index(month) + 1

       df = df[df['month'] == month]

    if day != 'all':
       df = df[df['day_of_week'] == day.title()]



    return df


def time_stats(df):
    """Displays statistics on the most frequent times of travel."""

    print('\nCalculating The Most Frequent Times of Travel...\n')
    start_time = time.time()

    # TO DO: display the most common month
    months = ['january', 'february', 'march', 'april', 'may', 'june']
    month = df['month'].mode()[0]
    print(f'The most common month is: {months[month - 1]}')

    # TO DO: display the most common day of week
    day = df['day_of_week'].mode()[0]
    print(f'The most common day is: {day}')

    # TO DO: display the most common start hour
    df['hour'] = df['Start Time'].dt.hour
    popular_hour = df['hour'].mode()[0]
    print(f'The most common start hour is: {popular_hour}')

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def station_stats(df):
    """Displays statistics on the most popular stations and trip."""

    print('\nCalculating The Most Popular Stations and Trip...\n')
    start_time = time.time()

    # TO DO: display most commonly used start station
    pss = df['Start Station'].mode()[0]
    print(f'The most popular start station is: {pss}')

    # TO DO: display most commonly used end station
    pes = df['End Station'].mode()[0]
    print(f'The most popular end station is: {pes}')

    # TO DO: display most frequent combination of start station and end station trip
    pt = df['Start Station'] + ' to ' + df['End Station']
    print(f'The most popular trip is: from {pt.mode()[0]}')


    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def trip_duration_stats(df):
    """Displays statistics on the total and average trip duration."""

    print('\nCalculating Trip Duration...\n')
    start_time = time.time()

    # TO DO: display total travel time
    ttd = (pd.to_datetime(df['End Time']) - pd.to_datetime(df['Start Time'])).sum()
    days = ttd.days
    hours = ttd.seconds // (60*60)
    minutes = ttd.seconds % (60*60) // 60
    seconds = ttd.seconds % (60*60) % 60
    print(f'Total travel time is: {days} days {hours} hours {minutes} minutes {seconds} seconds')

    # TO DO: display mean travel time
    atd = (pd.to_datetime(df['End Time']) - pd.to_datetime(df['Start Time'])).mean()
    days = atd.days
    hours = atd.seconds // (60*60)
    minutes = atd.seconds % (60*60) // 60
    seconds = atd.seconds % (60*60) % 60
    print(f'Average travel time is: {days} days {hours} hours {minutes} minutes {seconds} seconds')


    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def user_stats(df):
    """Displays statistics on bikeshare users."""

    print('\nCalculating User Stats...\n')
    start_time = time.time()

    # TO DO: Display counts of user types
    print(df['User Type'].value_counts())
    print('\n\n')

    # TO DO: Display counts of gender
    if 'Gender' in(df.columns):
              print(df['Gender'].value_counts())
              print('\n\n')

    # TO DO: Display earliest, most recent, and most common year of birth
    if 'Birth Year' in(df.columns):
              year = df['Birth Year'].fillna(0).astype('int64')
              print(f'Earliest birth year is: {year.min()}\n most recent is: {year.max()}\n and most comon birth year is: {year.mode()[0]}')


    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def display_raw_data(df):
    """Ask the user if he wants to display the raw data and print 5 rows at time"""
    raw = input('\nWould you like to display raw data?\n')
    if raw.lower() == 'yes':
        count = 0
        while True:
            print(df.iloc[count: count+5])
            count += 5
            ask = input('Next 5 raws?')
            if ask.lower() != 'yes':
                break


def main():
    while True:
        city, month, day = get_filters()
        df = load_data(city, month, day)

        time_stats(df)
        station_stats(df)
        trip_duration_stats(df)
        user_stats(df)
        display_raw_data(df)

        restart = input('\nWould you like to restart? Enter yes or no.\n')
        if restart.lower() != 'yes':
            break


if __name__ == "__main__":
	main()


### Date created
24 February 2021

### Project Title
Bike Share Project - Python

### Description
We make use of Python to explore data related to bike share systems for three major cities in the United States—Chicago, New York City, and Washington

### Files used
chicago.read_csv
new_york_city.read_csv
washington.read_csv

### credits
https://www.youtube.com/watch?v=5Fl5MPaFrH4

# Exploring Hacker News

In this project, we'll work with a data set of submissions to popular technology site Hacker News.We're specifically interested in posts whose titles begin with either Ask HN or Show HN. Users submit Ask HN posts to ask the Hacker News community a specific question

We'll compare these two types of posts to determine the following:

Do Ask HN or Show HN receive more comments on average?
Do posts created at a certain time receive more comments on average?
Let's start by importing the libraries we need and reading the data set into a list of lists.




```python
from csv import reader

opened_file = open('hacker_news.csv')
read_file = reader(opened_file)
hn = list(read_file)

print(hn[0:5]) #displaying first 5 rows
```

    [['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at'], ['12224879', 'Interactive Dynamic Video', 'http://www.interactivedynamicvideo.com/', '386', '52', 'ne0phyte', '8/4/2016 11:52'], ['10975351', 'How to Use Open Source and Shut the Fuck Up at the Same Time', 'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/', '39', '10', 'josep2', '1/26/2016 19:30'], ['11964716', "Florida DJs May Face Felony for April Fools' Water Joke", 'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/', '2', '1', 'vezycash', '6/23/2016 22:20'], ['11919867', 'Technology ventures: From Idea to Enterprise', 'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429', '3', '1', 'hswarna', '6/17/2016 0:01']]


Notice that the first list in the inner lists contains the column headers, and the lists after contain the data for one row. In order to analyze our data, we need to first remove the row containing the column headers. Let's remove that first row next.

1) Extract the first row of data, and assign it to the variable headers.

2) Remove the first row from hn.

3) Display headers.

4) Display the first five rows of hn to verify that you removed the header row properly.



```python
headers = hn[0]
hn = hn[1:]

print(hn[:5])
```

    [['12224879', 'Interactive Dynamic Video', 'http://www.interactivedynamicvideo.com/', '386', '52', 'ne0phyte', '8/4/2016 11:52'], ['10975351', 'How to Use Open Source and Shut the Fuck Up at the Same Time', 'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/', '39', '10', 'josep2', '1/26/2016 19:30'], ['11964716', "Florida DJs May Face Felony for April Fools' Water Joke", 'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/', '2', '1', 'vezycash', '6/23/2016 22:20'], ['11919867', 'Technology ventures: From Idea to Enterprise', 'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429', '3', '1', 'hswarna', '6/17/2016 0:01'], ['10301696', 'Note by Note: The Making of Steinway L1037 (2007)', 'http://www.nytimes.com/2007/11/07/movies/07stein.html?_r=0', '8', '2', 'walterbell', '9/30/2015 4:12']]


Calculating the Average Number of Comments for **Ask HN** and **Show HN** Posts


```python
ask_posts = []
show_posts = []
other_posts = []

for row in hn:
    title = row[1]
    title_lower_case = title.lower()#changes title to lower case
    #print(title)

    if title_lower_case.startswith('ask hn'):
        ask_posts.append(row) 
        #print(ask_posts)
    
    elif title_lower_case.startswith('show hn'):
        show_posts.append(row)
        #print(show_posts)
        
    else:
        other_posts.append(row)
        
print("Number of posts with'ask hn':",len(ask_posts))
print ("Number of posts with'show hn':",len(show_posts))
print ("Other posts':",len(other_posts))
```

    Number of posts with'ask hn': 1744
    Number of posts with'show hn': 1162
    Other posts': 17194



```python
print(ask_posts[:5])
```

    [['12296411', 'Ask HN: How to improve my personal website?', '', '2', '6', 'ahmedbaracat', '8/16/2016 9:55'], ['10610020', 'Ask HN: Am I the only one outraged by Twitter shutting down share counts?', '', '28', '29', 'tkfx', '11/22/2015 13:43'], ['11610310', 'Ask HN: Aby recent changes to CSS that broke mobile?', '', '1', '1', 'polskibus', '5/2/2016 10:14'], ['12210105', 'Ask HN: Looking for Employee #3 How do I do it?', '', '1', '3', 'sph130', '8/2/2016 14:20'], ['10394168', 'Ask HN: Someone offered to buy my browser extension from me. What now?', '', '28', '17', 'roykolak', '10/15/2015 16:38']]


Next, let's determine if **ask posts** or **show posts** receive more comments on average.


```python
total_ask_comments = 0

for comments in ask_posts:
    user_com = int(comments[4])
    total_ask_comments += user_com
    avg_ask_comments =  total_ask_comments / len(ask_posts)
print('Average comments on asked posts:', avg_ask_comments)

total_show_comments = 0

for comments in show_posts:
    shown_com = int(comments[4])
    total_show_comments += shown_com    
    avg_show_comments = total_show_comments / len(show_posts)
print('Average number of comments on show posts:',avg_show_comments)

diff = avg_ask_comments - avg_show_comments
print('difference between asked vs commented posts:',diff)

```

    Average comments on asked posts: 14.038417431192661
    Average number of comments on show posts: 10.31669535283993
    difference between asked vs commented posts: 3.7217220783527303


Asked posts receives more comments on avg, indicated by a difference of 3.72

Next, we'll determine if ask posts created at a certain time are more likely to attract comments. We'll use the following steps to perform this analysis:

1) Calculate the amount of ask posts created in each hour of the day, along with the number of comments received.

2) Calculate the average number of comments ask posts receive by hour created.


```python
counts_by_hr = {}
comments_by_hr = {}

import datetime as dt
result_list = []


for row in ask_posts:
    result_list.append([row[6],int(row[4])])

for row in result_list:
    hr = row[0]
    num_comments = row[1]
    date_created = dt.datetime.strptime(hr,'%m/%d/%Y %H:%M')
    hr_created = date_created.strftime('%H')
    
    if hr_created not in counts_by_hr:
        counts_by_hr[hr_created] = 1
        comments_by_hr[hr_created] = num_comments
        
    else:
        counts_by_hr[hr_created] += 1
        comments_by_hr[hr_created] += num_comments
        
#print('Comments by hour:','\n',comments_by_hr)
#print('\n')
#print('Counts by hour:','\n',counts_by_hr)


#for key,value in comments_by_hr.items():
#    print(key,'-',value)
    
    
```

#### Comments by hour: 

'09': 251, '13': 1253, '10': 793, '14': 1416, '16': 1814, '23': 543, '12': 687, '17': 1146, '15': 4477, '21': 1745, '20': 1722, '02': 1381, '18': 1439, '03': 421, '05': 464, '19': 1188, '01': 683, '22': 479, '08': 492, '04': 337, '00': 447, '06': 397, '07': 267, '11': 641


#### Counts by hour: 

'09': 45, '13': 85, '10': 59, '14': 107, '16': 108, '23': 68, '12': 73, '17': 100, '15': 116, '21': 109, '20': 80, '02': 58, '18': 109, '03': 54, '05': 46, '19': 110, '01': 60, '22': 71, '08': 48, '04': 47, '00': 55, '06': 44, '07': 34, '11': 58

Next, we'll use these two dictionaries to calculate the average number of comments for posts created during each hour of the day.


```python
avg_by_hr = []

for key in counts_by_hr:
    avg_com = comments_by_hr[key] / counts_by_hr[key]
    avg_by_hr.append([key,avg_com])                      
                     
for key in counts_by_hr:
    avg_posts = comments_by_hr[key]/counts_by_hr[key]
    avg_by_hr.append([key,avg_posts]) 

print(*avg_by_hr, sep = '\n')
```

    ['09', 5.5777777777777775]
    ['13', 14.741176470588234]
    ['10', 13.440677966101696]
    ['14', 13.233644859813085]
    ['16', 16.796296296296298]
    ['23', 7.985294117647059]
    ['12', 9.41095890410959]
    ['17', 11.46]
    ['15', 38.5948275862069]
    ['21', 16.009174311926607]
    ['20', 21.525]
    ['02', 23.810344827586206]
    ['18', 13.20183486238532]
    ['03', 7.796296296296297]
    ['05', 10.08695652173913]
    ['19', 10.8]
    ['01', 11.383333333333333]
    ['22', 6.746478873239437]
    ['08', 10.25]
    ['04', 7.170212765957447]
    ['00', 8.127272727272727]
    ['06', 9.022727272727273]
    ['07', 7.852941176470588]
    ['11', 11.051724137931034]
    ['09', 5.5777777777777775]
    ['13', 14.741176470588234]
    ['10', 13.440677966101696]
    ['14', 13.233644859813085]
    ['16', 16.796296296296298]
    ['23', 7.985294117647059]
    ['12', 9.41095890410959]
    ['17', 11.46]
    ['15', 38.5948275862069]
    ['21', 16.009174311926607]
    ['20', 21.525]
    ['02', 23.810344827586206]
    ['18', 13.20183486238532]
    ['03', 7.796296296296297]
    ['05', 10.08695652173913]
    ['19', 10.8]
    ['01', 11.383333333333333]
    ['22', 6.746478873239437]
    ['08', 10.25]
    ['04', 7.170212765957447]
    ['00', 8.127272727272727]
    ['06', 9.022727272727273]
    ['07', 7.852941176470588]
    ['11', 11.051724137931034]



```python
swap_avg_by_hr = []

for rows in avg_by_hr:
    swap_avg_by_hr.append([rows[1],rows[0]])
```


```python
sorted_swap = sorted(swap_avg_by_hr,reverse=True)
print('Top 5 Hours for Ask Posts Comments:',*sorted_swap[0:5], sep = '\n')


```

    Top 5 Hours for Ask Posts Comments:
    [38.5948275862069, '15']
    [38.5948275862069, '15']
    [23.810344827586206, '02']
    [23.810344827586206, '02']
    [21.525, '20']



```python
for row in sorted_swap[:5]:
    hr_format = dt.datetime.strptime(str(row[1]),'%H')
    time_format = hr_format.strftime('%H:%M')
    time_avg = '{}: {:.2f} average per post.'
    print(time_avg.format(time_format,row[0]))
    
```

    15:00: 38.59 average per post.
    15:00: 38.59 average per post.
    02:00: 23.81 average per post.
    02:00: 23.81 average per post.
    20:00: 21.52 average per post.


Which hours should you create a post during to have a higher chance of receiving comments? Refer back to the documentation for the data set to convert the times to the time zone you live in. Write a markdown cell explaining your findings.

The top three times (Pacific Daylight Time) during the day to create a post and recieve comments are:

* 5am -6 am
* 12pm - 1 pm
* 5pm - 6pm
* 11pm - 12pm

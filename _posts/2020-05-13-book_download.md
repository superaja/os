---
layout: post
title: "Downloading Freely Available Textbooks with Python"
description: "Springer Link made their coveted text books available for download. A simple code to download them using python web-scraping libraries"
thumb_image: "oslogo.png"
tags: [misc, coding]
---

I am an avid book reader. I prefer a physical book over digital as I like the feel of a book in my hand and I would rather spend on a book rather than a license to read a book. So when [Springer announced the availability of some of most coveted textbooks to freely download](https://www.springernature.com/gp/researchers/the-source/blog/blogposts-life-in-research/access-textbooks-for-free-during-the-coronavirus-lockdown/17897628?utm_source=springer&utm_medium=banner&utm_content=leaderboard&utm_campaign=CMTL_1_RS_CMTL_Free%20Textbooks_Source_Springer_Banner_MPU_Leader){:target="_blank"} during the lockdown to support students and teachers, I had mixed feelings. I owe some of these books but I also wanted to sample others that are in different fields (e.g. Neurobiology). If I really like what I sample, I can buy those physical book. Finally after some argument with myself, I decided to go ahead and download them.

To make life easy, I automated the process of downloading all of the freely available books. Some of the decisions I made - 

1. Use Python's BeautifulSoup
2. Use Google Drive to download the PDFs
3. Use Google Colab Notebook

There are some complexities that had to be addressed. Firstly Springer provided an [excel spreadsheet](https://resource-cms.springernature.com/springer-cms/rest/v1/content/17858272/data/v5){:target="_blank"} with details of the books and mostly importantly the download URL. But the download URL is not exactly what I was expecting. It was a link to the home page of a particular book and not the book location. Secondly, there was no specific pattern I could find between the given URL in the spreadsheet and the download URL. This meant I had to use web-scraping to get the download link of the book.

Example: Fundamentals of Power Electronics book's home-page URL is `https://link.springer.com/book/10.1007%2Fb100747` but the location of the PDF as provided on the home page is `https://link.springer.com/content/pdf/10.1007%2Fb100747.pdf`. Do not get fooled by the pattern `10.1007%2Fb100747.pdf` in this example. This is not necessarily applicable across all the books. 

After looking at a few home page URLs, I was able to locate an `href` and a class `data-track-action` in the following `<a href="/content/pdf/10.1007%2Fb100747.pdf" target="_blank"
											data-track-action="Book download - pdf" data-track-label="">` that was common and uniquely identified the link I needed to set-up BeautifulSoup search parameters.

**An important note**: I am vehemently opposed to using web-scraping for illegal / blantant downloding of content that is created by hard-working people and owned by others - Books are no exception. Knowledge is priceless and paying a few dollars for a physical copy (or digital) only increases the quality of books as smart people will be encouraged to write and publish. 

The following is the code. The notebook can be accessed [here](https://github.com/superaja/notebooks/blob/master/book_download.ipynb){:target="_blank"}

```
# Module Import
import requests
import pandas as pd
from bs4 import BeautifulSoup
import re
```
```
# mount google drive
from google.colab import drive
drive.mount('/content/drive')
```
    Go to this URL in a browser: https://accounts.google.com/o/oauth2/auth?client_id=...
    
    Enter your authorization code:
    ··········
    Mounted at /content/drive

```
# change your location here for the Springer Book Data which is linked above.
df = pd.read_excel('/content/drive/My Drive/Books/Springer/Free+English+textbooks.xlsx')
df.head(5)
```
```
# get the urls and book titles and combine them into a list
url_link = list(df['OpenURL'].iloc[0:])
book_title = df['Book Title'].iloc[0:]
r = list(zip(book_title, url_link))
```

```
# PDF Writer function
def writer(name,download_url):
  # name: Name of the Book
  # download_url: the springer location for the book
  headers = {}
  r = requests.get(download_url, stream = True, headers=headers)
  # your own target location below
  with open('/content/drive/My Drive/Books/Springer/'+name+'.pdf',"wb") as pdf: 
    for chunk in r.iter_content(chunk_size=1024): 
      # writing one chunk at a time to pdf file 
      if chunk: 
        pdf.write(chunk) 
```

```
#main execution
link = 'https://link.springer.com'
for url in r:
  payload = {}
  headers= {}
  response = requests.request("GET", url[1], headers=headers, data = payload)
  #response = requests.get(url)
  soup= BeautifulSoup(response.text, "html.parser")
  # find the value of href in the 'a' tag where the class 'data-track-action' starts with "Book"
  first_pass = soup.find('a', {'data-track-action': re.compile('^Book')})
  download_url = link + str(first_pass['href'])
  name = url[0].replace(" ", "")
  print ("Downloading Book" + name)
  writer(name, download_url)
  print ("Download Complete")
```

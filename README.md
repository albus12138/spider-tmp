# spider-tmp

import urllib2

from BeautifulSoup import BeautifulSoup


BASE_URL = "http://www.nkzx.cn"


#Split Article Content From HTML
def get_content(html):
    article = soup.find(id=container').find(id='main').find(id='content').find('table').find('tr').findAll('td')[1].findAll('div')[2]
    title = article.findAll('div')[0].string
    info = article.findAll('div')[1].string.split("    |    ")
    pub_from = info[0].split("：")[1]
    pub_date = info[0].split("：")[1].split("-")
    content = article.findAll('div')[2].string  #TODO:Maybe fail.What The F**K!
    return {"pub_from":pub_from, "pub_date":pub_date, "content":content}


#Get Website Banners
response = urllib2.urlopen(BASE_URL+"/")
html = response.read()
soup = BeautifulSoup(html)
banner = soup.find(id='banner').find(id='nav').findAll('li')[1:]

#Walk Banners & Collect Articles
for item in banner:
    li_list = item.find('ul').findAll('li')
    for li in li_list:
        prefix = li.find('a').string
        href = li.find('a').get('href')
        response = urllib2.urlopen(BASE_URL+href)
        html = response.read()
        soup = BeautifulSoup(html)

        #Article or List?
        sign = soup.find(id=container').find(id='main').find(id='content').find('table').find('tr').findAll('td')[1].findAll('div')[1]
        if sign.get("id") == 'items':
            #Deal List items
            list = soup.find(id=container').find(id='main').find(id='content').find('table').find('tr').findAll('td')[1].findAll('div')[1]
            ul_list = list.findAll('ul')
            for ul in ul_list:
                href = ul.find('li').find('a').get('href')
                response = urllib2.urlopen(BASE_URL+href)
                html = response.read()
                get_content(html)
        else:
            get_content(html)

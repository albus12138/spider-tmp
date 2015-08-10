# spider-tmp

import urllib2

from BeautifulSoup import BeautifulSoup


BASE_URL = "http://www.nkzx.cn"


#Split Article Content From HTML
def get_content(html):
    soup = BeautifulSoup(html)
    div = soup.findAll('div')
    title = div[-5]
    info = div[-4].string.split("&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;")
    pub_from = info[0].split("：")[1]
    pub_date = info[1].split("：")[1].split("-")
    content = soup.findAll('div')[-3]
    result = re.sub(r'<[^>]+>', '', str(content))
    result = re.sub(r'^\n$', '', result)
    result = re.sub(r' ', '', result)
    content = re.sub(r'&nbsp;', ' ', result)
    if u"正在建设" in content:
        return {"available": False}
    return {"pub_from":pub_from, "pub_date":pub_date, "content":content, "available":True}


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
                result = get_content(html)
                print result
        else:
            result = get_content(html)
            print result

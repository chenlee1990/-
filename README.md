# -
爬取tripadvisor上的旅游地名称及点评数量
from  bs4 import BeautifulSoup
import requests,openpyxl,time,string,re
t = time.time()

url = 'https://www.tripadvisor.cn/Attractions-g60763-Activities-New_York_City_New_York.html'
urls=['https://www.tripadvisor.cn/Attractions-g60763-Activities-oa{}-New_York_City_New_York.html#FILTERED_LIST'\
          .format(str(i)) for i in range(30,930,30)]
def get_attractions(url,data=None):
    wb_data = requests.get(url)
    soup = BeautifulSoup(wb_data.text,'lxml')
    titles = soup.select('div.listing_title >a[target="_blank"]')
    talks = soup.select(' div > div > div > div.listing_info > div > div > div > span.more > a')
    datas = []
    for title,talk in zip(titles,talks):
        data = {
            'title':title.get_text(),
            'talk':talk.get_text().strip()
        }
        datas.append(data)
    return datas
data_numbers = []
for single_url in urls:
    data_numbers.append(get_attractions(single_url))

print(data_numbers)
wb = openpyxl.Workbook()
sh = wb.active
j=0
try:
    for i_1 in range(1,len(data_numbers)+1):
        j+=1
        for i_2 in range(1,len(data_numbers[j-1])+1):
            print(len(data_numbers[j-1]))
            sh.cell(i_2+30*(j-1),1,str(data_numbers[i_1-1][i_2-1]['title']))
            talksRegex = re.compile(r'\d,?\d*')
            re_talk = talksRegex.search(str(data_numbers[i_1-1][i_2-1]['talk']))
            if re_talk.group().find(','):
                sh.cell(i_2+30*(j-1),2,int(re_talk.group().replace(',','')))
except Exception as e:
    print('Error')
else:
    wb.save('C:/Users/chenl/Desktop/pycharm.xlsx')

    t = time.time() - t
    print(t)


'''
wb_data = requests.get(urls)
soup = BeautifulSoup(wb_data.text,'lxml')
titles = soup.select('div.listing_title >a[target="_blank"]')
imgs = soup.select('img[width="180"]')
# talks = soup.select('#ATTR_ENTRY_267031 > div > div > div > div.listing_info > div.listing_rating > div:nth-child(2) > div > span.more > a')
talks = soup.select(' div > div > div > div.listing_info > div > div > div > span.more > a')
# ranks = soup.select(' div > div > div > div.listing_info > div.listing_rating ')
# print(ranks)

for title,img,talk in zip(titles,imgs,talks):
    data = {
        'title':title.get_text(),
        'img':img.get('src'),
        'talk':talk.get_text().strip()
    }
    print(data)

headers = {
    'User_Agent':'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.169 Safari/537.36',
    'Cookie':'RT=%1%enc%3AEt9ME1l%2FVUUV53RTumfHydP2%2FHCK25UBCnQCgP223%2BpLKjmtQ%2FrLtsdajOAZuH48Nox8JbUSTxk%3D'
}

urls_saves = 'https://www.tripadvisor.cn/Saves/1678071'
wb_data = requests.get(urls_saves,headers=headers)
soup = BeautifulSoup(wb_data.text,'lxml')
titles = soup.select('div.location_summary')
imgs = soup.select(' a.thumbnail')

print(titles)
'''

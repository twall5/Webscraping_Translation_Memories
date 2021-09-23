import requests
from selenium import webdriver
from bs4 import BeautifulSoup
from bs4.element import Comment

websites = ["https://cadw.gov.wales/",
           "https://senedd.wales/",
           "https://digitalpublicservices.gov.wales/",
           "https://mentalhealthreviewtribunal.gov.wales/",
           "https://residentialpropertytribunal.gov.wales/",
           "https://specialeducationalneedstribunal.gov.wales/",
           "https://rcahmw.gov.uk/",
           "https://welshlanguagetribunal.gov.wales/"]

def tag_visible(element):
    if element.parent.name in ['style', 'script', 'head',  'meta', '[document]']:
        return False
    if isinstance(element, Comment):
        return False
    return True

def get_text(url):
    try:
        req = requests.get(url)
    except:
        return "ERROR"
    data = req.text
    soup = BeautifulSoup(data, 'html.parser')
    texts = soup.find_all(text=True)
    visibleTexts = filter(tag_visible, texts)
    return u" ".join(t.strip() for t in visibleTexts)

def replace_unicode(text):
    uniChars = [u"\xa0", u"\u202f"]
    for char in uniChars:
        newText = text.replace(char, u" ")
    return newText

def write_to_file(url, enText, cyText):
    # Removing forward slashes and https text to create files
    fileName = url[7:].replace('/', '_')
    enFile = open('D:\MSc Data Science\Dissertation Project\ScrapedText\\' + fileName + '.txt', "a", encoding="utf-8")
    enFile.write(enText)
    enFile.close()
    cyFile = open('D:\MSc Data Science\Dissertation Project\ScrapedText\\' + fileName + 'cy.txt', 'a', encoding="utf-8")
    cyFile.write(cyText)
    cyFile.close()

for site in websites:
    
    webpages = [site]
    # Links to the Welsh version of each website which must not be included in the crawled webpages
    cyLinks = ["https://cadw.llyw.cymru/", "https://senedd.cymru/",
              "https://gwasanaethaucyhoeddusdigidol.llyw.cymru/",
              "https://tribiwnlysadolyguiechydmeddwl.llyw.cymru/",
              "https://tribiwnlyseiddopreswyl.llyw.cymru/",
              "https://tribiwnlysanghenionaddysgolarbennig.llyw.cymru/",
              "https://cbhc.gov.uk/", "https://tribiwnlysygymraeg.llyw.cymru/"]
    # Accessing the website home page
    req = requests.get(site)
    # Parsing the HTML encoding
    bs = BeautifulSoup(req.text, 'html.parser')
    # Finding links to web pages linked from the front page and adding to list of pages
    for link in bs.find_all('a'):
        if 'href' in link.attrs:
            # Avoids the Welsh language web page
            if link.attrs['href'] not in cyLinks:
                if site in link.attrs['href']:
                    # Checks the web page link hasn't already been scraped
                    if link.attrs['href'] not in webpages:
                        newPage = link.attrs['href']
                        webpages.append(newPage)
    
    # Setting path and driver info 
    path = r'C:\webdrivers\geckodriver.exe'
    driver = webdriver.Firefox(executable_path=path)
    # For each web page
    for url in webpages:
        driver.get(url)
        # Access webpage and scrape visible text
        enText = get_text(url)
        # Find Welsh language button and press it
        try:
            toggleLang = driver.find_element_by_link_text('Cymraeg')
        except:
            try:
                toggleLang = driver.find_element_by_link_text('CYMRAEG')
            except:
                # Cannot find Welsh language button.
                continue
        toggleLang.click()
        # Obtain Welsh version of each webpage  
        cyUrl = driver.current_url
        # Access Welsh webpage and scrape visible text
        cyText = get_text(cyUrl)
        # Remove unicode characters from the text
        enText = replace_unicode(enText)
        cyText = replace_unicode(cyText)
        # Save texts to file
        write_to_file(site, enText, cyText)

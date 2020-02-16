# pythonscrape
Using Python Scrapy we create a spider to crawl websites to scrape and structure the data.
## Installation && usage
```bash
git init
```

```bash
git clone https://github.com/johnbroaddusivccv/pythonscrape.git
```

```bash
cd pythonscrape/
```

Make sure Python is installed 
```bash
python ––version
```
When using python it's important to create a virtual enviroment. A virtual enviroment is a tool that helps keep dependencies required by different projects separate by isolation.

To create a virtual enviroment bash the following:
* on windows 10
```bash
py -m venv venv
```
to activate the virtual enviroment bash the following:
```bash
source venv/scripts/activate
```
to deactivate the virtual enviroment bash the following:
```bash
deactivate
```
Use the package manager [pip](https://pip.pypa.io/en/stable/) to install the framework [Scrapy](scrapy.org).
* On windows 10
```bash
py -m pip install scrapy
```
* On Mac
```bash
pip install scrapy
```
### before you start scraping
I used the following to create the [postscrape](https://github.com/johnbroaddusivccv/pythonscrape/tree/master/postscrape) directory
```bash
scrapy startproject postscrape
```
Change directories into [postscrape](https://github.com/johnbroaddusivccv/pythonscrape/tree/master/postscrape)
```bash
cd postscrape/
```
Do this once more as I have named the directory inside of postscrape - postscrape.
```bash
cd postscrape/
```
Create a spider (In this repo I've already created one)[posts_spider.py](https://github.com/johnbroaddusivccv/pythonscrape/blob/master/postscrape/postscrape/spiders/posts_spider.py)
bash the following to create a new spider (you can not set the same name for different spiders):
```bash
cd spiders
```
```bash
touch quotes_spider.py
```
Insert the following into our new spider to scrape quotes:
```python
import scrapy


class QuotesSpider(scrapy.Spider):
    name = "quotes"

    def start_requests(self):
        urls = [
            'http://quotes.toscrape.com/page/1/',
            'http://quotes.toscrape.com/page/2/',
        ]
        for url in urls:
            yield scrapy.Request(url=url, callback=self.parse)

    def parse(self, response):
        page = response.url.split("/")[-2]
        filename = 'quotes-%s.html' % page
        with open(filename, 'wb') as f:
            f.write(response.body)
        self.log('Saved file %s' % filename)
```
* we iterate through the pages here:
```python
for url in urls:
            yield scrapy.Request(url=url, callback=self.parse)
```
  * This is where our spider begins to crawl (Request)
  ```python
  def parse(self, response):
        page = response.url.split("/")[-2]
        filename = 'quotes-%s.html' % page
        with open(filename, 'wb') as f:
            f.write(response.body)
        self.log('Saved file %s' % filename)
 ```
  * Parse() is a method that will be called to handle the response downloaded for each of the requests made.
  * Our Parse method method usually parses the response, extracting the scraped data as dicts and also finding new URLs to follow and creating new requests (Requests) from them.
To make our new spider crawl, go to the [postscrape](https://github.com/johnbroaddusivccv/pythonscrape/tree/master/postscrape) directory and run:
```bash
scrapy crawl quotes
```
* Notice how scrapy created two html files named quotes-1.html & quotes-2.html
- - - -
Now that's pretty cool that we got that mark-up down to the text editor but what about extracting certain data?
Lets use [The Scrapy Shell](https://docs.scrapy.org/en/latest/topics/shell.html#topics-shell)
bash the following:
* On Mac
```bash
scrapy shell 'http://quotes.toscrape.com/page/1/'
```
* On Windows 10
```bash
scrapy shell "http://quotes.toscrape.com/page/1/"
```
Now that you are in The shell, you can try selecting elements using CSS selectors.
run the following in the shell:
```shell
response.css('title')
```
It will return:
```bash
[<Selector xpath='descendant-or-self::title' data='<title>Quotes to Scrape</title>'>]
```
Now we aren't trying to see all that mark-up we just want the text in the title right?
run the following in the shell:
```bash
response.css('title::text').getall()
```
With that the shell returns:
```bash
['Quotes to scrape']
```
* We’ve added ::text to the CSS query, to mean we want to select only the text elements directly inside <title> element.
* We're using getall() to return a list, although there is only one title. To get just the first response use get()
- - - -
Lets get out of the shell using:
```bash
exit()
```
or <kbd>ctrl</kbd><kbd>c</bd>
- - - -
###Storing Scrapped Data
This is where the beauty of this technology comes in. You can turn this data you are scraping into JSON or a CSV.
* I often use this to merge languages, I use python for visualizing and pulling data and javascript for everything else.
* I'm able to use both python and javascript in a project when I turn the data I'm extracting into JSON.
* I'm able to import my data as a CSV into a database.
- - - -
Let's alter our function in the spider we just created.
```python
import scrapy


class QuotesSpider(scrapy.Spider):
    name = "quotes"
    start_urls = [
        'http://quotes.toscrape.com/page/1/',
        'http://quotes.toscrape.com/page/2/',
    ]

    def parse(self, response):
        for quote in response.css('div.quote'):
            yield {
                'text': quote.css('span.text::text').get(),
                'author': quote.css('small.author::text').get(),
                'tags': quote.css('div.tags a.tag::text').getall(),
            }
```
* Now not much has changed except we are now returning css selectors similar to the syntax we were using in the shell.
* We've defined each selector as text, author, and tags.
* Notice how we are only getting the first text & author, yet getting all the tags.
- - - -
bash the following:
```bash
scrapy crawl quotes -o quotes.json
```
This will generate a quotes.json file with all our data extracted serialized in JSON.
bash the following:
```bash
scrapy crawl quotes -o quotes.csv
```
This will generate a quotes.csv file with all our data extracted in a CSV format.
- - - -
Deactivate your virtual enviroment at a stopping point:
```bash
deactivate
```
[Scrapy](https://docs.scrapy.org/en/latest/) included this is just basic use of this framework.


## License
[MIT](https://choosealicense.com/licenses/mit/)





  
  



```
from bs4 import BeautifulSoup as bs
import requests

# defining header for requests
header = {"User-Agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/107.0.0.0 Safari/537.36"}


# list of product links, names, and number per page
products = [ ['https://www.homedepot.com/s/oven?NCNI-5&', 'oven', 24],
          ['https://www.homedepot.com/s/sink?NCNI-5', 'sink', 24],
          ['https://www.homedepot.com/s/fridge?NCNI-5', 'fridge', 24],
          ['https://www.homedepot.com/s/toaster?NCNI-5', 'toaster', 48],
          ['https://www.homedepot.com/s/utensils?NCNI-5', 'utensils', 48],
          ['https://www.homedepot.com/s/microwave?NCNI-5', 'microwave', 24],
          ['https://www.homedepot.com/s/knife%20set?NCNI-5', 'knifeset', 48],
          ['https://www.homedepot.com/s/plate?NCNI-5', 'plate', 48],
          ['https://www.homedepot.com/s/bowl?NCNI-5', 'bowl', 48],
          ['https://www.homedepot.com/s/cutting%20board?NCNI-5', 'cuttingboard', 48]
]

for product in products:
    # retrieve item information
    url = product[0]
    item = product[1]
    countPerPage = product[2]

    # counters for images and page number
    imageCount = 0
    pageCount = 0

    # while our total amount of saved images is fewer than 100
    while imageCount < 100:
        # change url according to page count
        if pageCount > 0:
            url = url + '&Nao=' + str(pageCount * countPerPage)

        # get url information with header
        r = requests.get(url, headers=header)

        # get page content from soup
        soup = bs(r.content, features="lxml")

        # select images
        images = soup.select('div img')

        # counter for images
        totalImageCount = 0

        # download each image
        for image in images:

            # make sure you don't go above 100 images within the loop
            if imageCount == 100:
                break

            # get the image url
            imageURL = images[totalImageCount]['src']

            # each usable photo has a portion "/svn/" and "/64_300", so only these images will be downloaded
            if 'svn' in imageURL and "64_300" in imageURL:

                imgData = requests.get(imageURL).content

                # name the file according to its item and number
                filepath = item + "-" + str(imageCount) + ".jpg"

                # download the image
                with open(filepath, 'wb') as handler:
                    handler.write(imgData)

                    #increase the count of usable images
                    imageCount += 1

            # increase total number 
            totalImageCount += 1

        # if we've reached the max images on the page
        if (imageCount % countPerPage) == 0 and imageCount != 0:
            pageCount += 1


```
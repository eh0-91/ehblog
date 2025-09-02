---
title: "Web scraping with JS"
date: 2024-03-08T16:18:13+02:00
draft: false
---

# Using Puppeteer to scrape a website

In this post we'll scrape a website with puppeteer! In our case it will be 'https://books.toscrape.com/',
great for trying stuff out since, in their own words, "We love being scraped!". It's a website that simulates
an online bookshop. Why scrape a website you might ask?
Well, in a business setting you could use it for price or news monitoring and general market research, getting some
info on your competitors :P
After having created a node project and installing puppeteer, we'll jump right in. Here is the whole script,
I commented quite a lot so it should be easy to follow without making interludes:

```js
import puppeteer from "puppeteer";

const getTitlesPrices = async () => {
  // Start a Puppeteer session
  const browser = await puppeteer.launch({
    headless: false,
    defaultViewport: null,
  });

  // Open a new page in the browser
  const page = await browser.newPage();

  let all_titles_prices = []

  for (let i = 1; i <= 50; i++) {
    // We've got 50 pages of books
    let website = `https://books.toscrape.com/catalogue/page-${i}.html`;
    // Open the website you want to scrape and wait until it loaded
    await page.goto(website, {
      waitUntil: "domcontentloaded",
    });

    const titles_prices = await page.evaluate(() => {
      // Get the titles of the books
      let titles = [];

      const books = document.querySelectorAll(".product_pod");
      books.forEach(e => titles.push(e.children[2]));

      // Get the prices of each book
      let prices = [];

      const price_color = document.querySelectorAll(".price_color");
      let re = /-?\d+(?:,\d{3})*(?:\.\d+)?/;
      price_color.forEach(e => prices.push(parseFloat(e.innerHTML.match(re))));

      // Return a merged array of titles and prices
      return titles.map((e, i) => [e["firstChild"]["title"], prices[i]]);
    });
    all_titles_prices.push(titles_prices);
  };
  // Print all titles and prices
  // console.log(all_titles_prices);

  // Show number of arrays: Should be 50 since we got one array for each page we scraped
  //console.log(all_titles_prices.length);

  // Determine cheapest/ most expensive book and average book price
  let cheapest_book = [];
  let most_expensive_book = [];
  let average_book_price = 0;

  for (let p = 0; p < 50; p++) {
    if (p === 0) {
      cheapest_book = all_titles_prices[p][0];
      most_expensive_book = all_titles_prices[p][0];
      average_book_price += all_titles_prices[p][0][1];
    }
    for (let b = 0; b < 20; b++) {
      average_book_price += all_titles_prices[p][b][1];

      if (cheapest_book[1] > all_titles_prices[p][b][1]) {
        cheapest_book = all_titles_prices[p][b];
        continue;
      };

      if (most_expensive_book[1] < all_titles_prices[p][b][1]) {
        most_expensive_book = all_titles_prices[p][b];
      };
    };
  };

  average_book_price = (average_book_price / 1000).toFixed(2);

  // Print the results
  console.log(`The cheapest book is:\n${cheapest_book[0]} - £${cheapest_book[1]}\n`);
  console.log(`The most expensive book is:\n${most_expensive_book[0]} - £${most_expensive_book[1]}\n`);
  console.log(`The average book price is £${average_book_price}`);
  // Close the browser
  await browser.close();
};

// Start the scraping
getTitlesPrices();

```

Not that hard, was it! And here are the results:

```
The cheapest book is:
An Abundance of Katherines - £10

The most expensive book is:
The Perfect Play (Play by Play #1) - £59.99

The average book price is £35.12

```

Seems reasonable! I won't go through the pages manually and check the results :P
I could refactor the code but it's fine for this toy project.



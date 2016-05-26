---
title: BS to AD date conversion and vice versa
tags:
  - Date Conversion
  - BS-AD Conversion
s: bs-to-ad-date-conversion-and-vice-versa
categories:
  - Programming
  - Javascript
author:
  name: Lekhnath Rijal
  link: 'http://lekhnathrijal.com.np'
comments: true
date: 2016-05-25 18:44:45
---
In this article we will learn to convert a date from A.D. to B.S. and vice versa using the `Javascript` programming language. <!-- more --> Although we are using `Javascript` for the date conversion it should not be much more tricky to [port](http://www.wikipedia.org/language_port) this application in some other languages out there.

{% asset_img cover.png 'AD-BS/BS-AD date conversion' %}

If you google [ad to bs conversion](http://www.google.com?q=ad+to+bs+conversion), you'll get bunch of links for web, mobile and desktop applications which converts the date for you, but if you wanted to know the actual logic behind this conversion mechanism then you are out of luck. I am here to provide you with some ideas just to make it work.

# Lets get started

## A.D. to B.S. Conversion

The first step involves picking a B.S. date and its corresponding A.D. date, which acts as the lower limit for our converter application. In this example application those values are:

``` javascript
START_DATE_BS = {year: 1990, month: 1, date: 1} //1st of Baisakh 1990
START_DATE_AD = new Date(1933, 3, 12); //12th of April 1933 which corresponds to one day before START_DATE_BS
```

Next we need to write the method which does the actual conversion, we call it `ad2bs` in our case. Give a quick glance at following code snippet, and don't freak out as I am going to tell you whats going in there. Treat it like an `unseen passage` given in board examinations and read it roughly not thoroughly at least for now.

{% gist 80e60f0988a51cd6e0c055a94682e998 converter.js %}

This method `convertDate.ad2bs` accepts an instance of javascript `Date` object as only parameter which is `adDate`. Now we calculate the difference between input date (ie. `adDate`) and start ad date (ie. `START_DATE_AD`) as follows:

``` javascript
daysFromStartDateAd = Math.floor( (adDate.getTime() - START_DATE_AD.getTime())/ ( 86400 * 1000) );
```

In case you stuck somewhere in above code:

``` javascript
daysFromStartDateAd = (adDate.getTime() - START_DATE_AD.getTime());
daysFromStartDateAd = daysFromStartDateAd / ( 86400 * 1000);
daysFromStartDateAd = Math.floor(daysFromStartDateAd);
```


Now the idea is to calculate how many years, months and days does `daysFromStartDateAd` means in B.S. Date system starting from `START_DATE_BS`, hence converting ad to bs.

The following sections tells you how to grab years, months and date out of `daysFromStartDateAd` in step by step fashion. Which also describes the `convertDate.ad2bs` method.

#### Determining the year
Assign `START_DATE_BS` to `bsDate`. Do a while loop until `daysFromStartDateAd > 0`. Each iteration adds one to bs year (ie. `bsDate.year`) if `daysFromStartDateAd` is greater than sum of days in bs year (ie. `daysInBsYear`). `daysInBsYear` is reduced from `daysFromStartDateAd` after increment bs year by one.

``` javascript
do {
    var daysInBsYear;

    daysInBsYear = array_sum(convertDate.BS_DAYS_IN_YEAR[bsDate.year.toString()]);

    if (daysInBsYear > daysFromStartDateAd) {

      //TODO: Calculate month and date

    } else {

      // ## DETERMINING THE YEAR ##

      //Calculate year
      bsDate.year++;
      daysFromStartDateAd -= daysInBsYear;

    }

  } while (daysFromStartDateAd > 0);
```

#### Determining the month
But if `daysFromStartDateAd` is less than `daysInBsYear` its time to calculate `month` and `date` rather than `year` out of `daysInBsYear`. Since there are 12 months in an year loop through each month using a for-loop. Each iteration adds one to the bs month (ie. `bsDate.month`) if `daysFromStartDateAd` is greater than number of days in bs month (ie. `daysInBsMonth`). `daysInBsMonth` is reduced from `daysFromStartDateAd` after increment bs month by one.

```javascript

if (daysInBsYear > daysFromStartDateAd) {

  for (var i = 0, daysInBsMonth; i < convertDate.MONTHS_IN_YEAR; i++) {
    daysInBsMonth = convertDate.BS_DAYS_IN_YEAR[bsDate.year.toString()][i];

    if (daysInBsMonth >= daysFromStartDateAd) {

      //TODO: Calculate date

    } else {

      // ## DETERMINING THE MONTH ##

      //Calculate month
      bsDate.month++;
      daysFromStartDateAd -= daysInBsMonth;

    }
  }

} else {

  //Calculate year
  ...
}
```

#### Determining the date
But if `daysFromStartDateAd` is less than **or** equals to `daysInBsMonth` then its the bs date value (ie. `bsDate.date = daysFromStartDateAd`).
Assign `null` or `0` to `daysFromStartDateAd` to  exit from the while loop. Then we break out of the for loop immediately after assignment.


```javascript
if (daysInBsMonth >= daysFromStartDateAd) {

  // ## DETERMINING THE DATE ##

  //Calculate date
  bsDate.date = daysFromStartDateAd;
  daysFromStartDateAd = 0;
  break;

}
```

Believe it or not, the conversion is complete! Return `bsDate` value and it's done.


## B.S. to A.D. conversion
Conversion from B.S to A.D. is much more simpler compared to A.D. to B.S. conversion, which is accomplished in `convertDate.bs2ad` method. All we need to do is calculate the difference between input bs date (ie. `bsDate`) and start bs date (ie. `START_DATE_BS`). This is done by summing days in years, days in months and the date value from `START_DATE_BS` to `bsDate`. Convert it to milliseconds by multiplying the result by `86400 * 1000`. Add it to number of milliseconds till start ad date (ie. `START_DATE_AD.getTime()`). Finally, construct the javascript date from that result to get the output.


## Live example

{% jsfiddle okn874cp 'result,resources' %}

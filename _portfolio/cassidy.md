---
title: "Cassidy - A better running log"
website: https://runcassidy.com
header:
  teaser: /assets/images/cassidy-teaser.png
excerpt: |
  Unsatisfied with the existing landscape of running log websites, my friend
  Cole Townsend and I built our own.
---
Cassidy, named for the protagonist in the novel _Once A Runner_, is a training log for runners and other athletes. It's a hobby project that I started with my friend [Cole Townsend](https://twnsnd.co) after growing tired of manipulating the spreadsheet that contained the past few years of my training. But anyone is welcome to [sign up](https://runcassidy.com) if interested!

For me, this project has been a fun opportunity to create something I'm passionate about and experiment with new technology and design patterns. It also kept me busy during long hours of quarantine and lockdown during the summer and fall of 2020 at the height of the COVID-19 pandemic.

## Features

Cassidy allows users to track their activies over time and view trends in measures like weekly mileage. By adding shoes and linking them to runs, users can also view how many miles have been run in each shoe. Users with a Strava account who connect it to Cassidy will see their activities posted automatically.

{% include figure image_path="/assets/images/cassidy-log.png" alt="The main page of the Cassidy training log showing recent activities and various stats." caption="The main Log page shows recent activities along with a summary of your past week and other stats." %}

Many more features are planned for Cassidy inspired by some of the useful and not-so-useful aspects of other online running logs and my trusty spreadsheet. These include better search tools and more analytics to help users view their progress and avoid setbacks in training.

## Technology

Cassidy uses a variety of modern technologies and libraries on both the front end (primarily developed by Cole) and the API (primarily developed by me).

### Front End

- [React](https://reactjs.org)
- [TypeScript](https://www.typescriptlang.org)
- [Apollo Client (GraphQL)](https://www.apollographql.com/docs/react/)
- [Auth0](https://auth0.com)
- [Rebass](https://rebassjs.org)
- [AWS Amplify](https://aws.amazon.com/amplify/)

### API

- [NestJS](https://nestjs.com)
- [GraphQL](https://graphql.org)
- [TypeORM](https://typeorm.io/#/)
- [Serverless](https://www.serverless.com)
- [AWS Lambda](https://aws.amazon.com/lambda/)
- [AWS Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/)

# FAQ

## What is a 	🤖

a bot in short is a automated process which submits your forms or executes actions.
the goal of the bot might be:
  - DDos [Wikipedia](https://en.wikipedia.org/wiki/Denial-of-service_attack)
  - Enhance chances of winning (Sweapstakes)
  - Manipulate data (Polls)
  - Search Engines / Crawlers
The range of bot technics varies alot, some are just simple scripts and on the other end of the spectrum you have high sophisticated
bots that try their best to be not deteced and act as close as humans.

Search Engine Bots are the exception, they are generally considered "good" bots - but still are bots

  - See [Our approach to detect/classify bots](ml.md)

  # GDRP
  as a 🇪🇺 native company we play strongly by the rules of GDPR.
  our number one goal is to collect as less as possible data. only the required one to classify bots.
  data is not long term stored.


    - no cookie / localstorage
    - no cross page-impression-tracking
    - behavioral data is anonmized on the client side already
    - IP / Connection data is stored anonmized by removing the last 2 blocks
      - e.g: `8.8.8.8` is stored (for statistics/ml...) as `8.8` same for ipv6 (`2001:db8:3333:4444:5555:6666:7777:8888` -> `2001:db8:3333:4444:5555:6666`

  # Detecting/Classfing bots

  to detect and classify bots we use various layers some of them are - only anonmized values are stored:

    - client side browser classification
    - behavioral: mouse movement, keyboard timings
    - system: timing of different parts
    - machine learning to generate a likelhood-to-be-human score.

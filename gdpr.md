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



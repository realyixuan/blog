# Take an example

- The sample code

~~~Python
import logging

# create logger
logger = logging.getLogger('simple_example')
logger.setLevel(logging.DEBUG)

# create console handler and set level to debug
ch = logging.StreamHandler()
ch.setLevel(logging.DEBUG)

# create formatter
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')

# add formatter to ch
ch.setFormatter(formatter)
# add filter to ch
ch.addFilter(logging.Filter('simple_example'))

# add ch to logger
logger.addHandler(ch)

# 'application' code
logger.info('info message')

~~~

- diagram

~~~
      +-----------------------------+
      | logger.info('info message') |
      +-----------------------------+
                 |
                 | .log() calling
 [Logger-part]   v
 +------------------------------------------------------------------------------------------------------------------------------------------------------+
 |                                                                                                                                                      |
 |               |                                                                                                                                      |
 |               v                                                                                                                                      |
 |                                                                                                                                                      |
 |      +----------------------------+                                                                                                                  |
 |      | create <LogRecord> object  |                                                                                                                  |
 |      +----------------------------+                                                                                                                  |
 |               |                                                                                                                                      |
 |               |                                                                                                                                      |
 |               |                              [Handler-part]                                                                                          |
 |               |                              +----------------------------------------------------------------------------------------------------+  |
 |               |                              |                                                                                                    |  |
 |               v                              |        |                                                                                           |  |
 |                                              |        v                                                                                           |  |
 |       +--------------------+                 |  +--------------------+                                                                            |  |
 |       | handle <LogRecord> | ------------>   |  | filter <LogRecord> |  <-------> [Filter-part]                                                   |  |
 |       +--------------------+                 |  +--------------------+                                                                            |  |
 |               |                              |        |                                                                                           |  |
 |               |                              |        |                                                                                           |  |
 |               |                              |        |                                +----                                                      |  |
 |               |                              |        |                                |                                                          |  |
 |               |                              |        |                                |             |                                            |  |
 |               |                              |        v                                |             v                                            |  |
 |               |                              |  +------------------+                   |  +--------------------------+                            |  |
 |               |                              |  | emit <LogRecord> |   ------------->  |  | msg = format <LogRecord> | <-------> [Formatter-part] |  |
 |               |                              |  +------------------+                   |  +--------------------------+                            |  |
 |               |                              |        |                                |             |                                            |  |
 |               |                              |        |                                |             | msg                                        |  |
 |               |                              |        |                                |             v                                            |  |
 |               |                              |        |                                |       +-----------------+                                |  |
 |               |                              |        |                                |       |   write <msg>   |                                |  |
 |               |                              |        |                                |       +-----------------+                                |  |
 |               |                              |        |                                |             |                                            |  |
 |               |                              |        |                                |             v                                            |  |
 |               |                              |        |                                |                                                          |  |
 |               |                              |        |                                +----                                                      |  |
 |               |                              |        v                                                                                           |  |
 |               |                              |                                                                                                    |  |
 |               |                              |                                                                                                    |  |
 |               |                              +----------------------------------------------------------------------------------------------------+  |
 |               |                                                                                                                                      |
 |               |                                                                                                                                      |
 |               v                                                                                                                                      |
 |              END                                                                                                                                     |
 |                                                                                                                                                      |
 +------------------------------------------------------------------------------------------------------------------------------------------------------+
~~~

# my thoughts

Here logging take composition instead of inheritance, we can arbitrary composite logger, handler and filter to get a specific logger with certain specific features. It's more dynamic and flexible.


## See also

[The Composition Over Inheritance Principle](https://python-patterns.guide/gang-of-four/composition-over-inheritance/)


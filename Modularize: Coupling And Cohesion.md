# Modularize: Coupling And Cohesion

    > Managing complexity is the most important technical topic in software development. In my view, it’s so important that Software’s Primary Technical Imperative has to be managing complexity.
    >
    > &mdash; &lt;&lt;Code Complete&gt;&gt; Steve McConnell


    > There are two general approaches to fighting complexity. ... The first approach is to eliminate complexity by making code simpler and more obvious. ...
    > 
    > The second approach to complexity is to encapsulate it, so that programmers can work on a system without being exposed to all of its complexity at once. This approach is called modular design.
    >
    > &mdash; &lt;&lt;A Philosophy of Software Design&gt;&gt; John Ousterhout

## Introduction

With experience increasing, all programmers will be gradually aware of: the more parts a piece of code has relationship with, the more difficult to make modifications. It is easy to get the feel: it is somewhat overwhelming when sevaral tasks(or problems) arise simultaneously, even more if it is parallel. Coordinating multiple things is much more difficult than just one thing, which is like a trick (imagine juggle 3 balls or more). As human beings, we are good at focusing on one thing, you should know [What multitasking does to your brain](https://www.youtube.com/watch?v=tMiyzuO1qMs). In software, it's similar that reducing the amount of relationship between modules will make development much easier. So independence of modules becomes an important topic in software design.

- **coupling**

There is a term, coupling, which is developed by Larry Constantine in the late 1960s as part of a structured design, to refer to the dependence **between modules**. But there is common misunderstanding, which oversimplifies the concept, just chopping system into small pieces that's all. In inconsequence, not only there is more bugs, but developing also becomes more inefficient.

    > Specifically, we suggest that the cost of implementing a computer system will be minimized when the parts of the problem are
    >
    > - manageably small
    > - solvable separately
    >
    > ...
    > 
    > Of course, many designers have made attempts to "Chop" a system into manageably small pieces; unfortunately, they have often found that implementation time increased rather than decreased. The key frequently lies in the second part of our stipulation above: The parts of the original problem must be solvable separately. In many computer systems, we find that this is not so: In order to implement part A of the solution to the problem, we have to know something about part B ... and in order to solve part B, we have to know something about part C.
    > 
    > &mdash; &lt;&lt;Structured Design&gt;&gt; Larry Constantine

    A simple way to judge the coupling between two modules:

    > The more that we must know of module B in order to understand module A, the more closely connected A is to B.
    >
    > &mdash; &lt;&lt;Structured Design&gt;&gt; Larry Constantine

As often said: Do one thing at one time, we need to do only one thing in one module. Now, how to define the "one":

    > By partitioning we mean the division of the problem into smaller subproblems, so that each subproblem will eventually correspond to a piece of the system. The questions are: Where and how should we divide the problem? Which aspects of the problem belong in the same part of the system, and which aspects belong in different parts?  Structured design answers these questions with two basic principles:
    > 
    > - Highly interrelated parts of the problem should be in the same piece of the system, i.e., things that belong together should go together.
    > - Unrelated parts of the problem should reside in unrelated pieces of the system. That is, things that have nothing to do with one another don't belong together.
    >
    > &mdash; &lt;&lt;Structured Design&gt;&gt; Larry Constantine

Even though we aim to lower the coupling, however, **as long as any module belongs to system, coupling to the system must exist in some form, more or less**. 

- **cohesion**

Cohesion is contrast with coupling, refers to degree to which **the elements inside a module** belong together. Namely, **cohesion indicates the degree of dependence between intramodule elements, and coupling otherwise is degree of dependence between intermodule elements**. Usually, high cohesion indicates loose coupling, and vice versa. This means: the one of targets of system design is to achieve the highest cohesion for each module. Usually speaking, paying attention on cohesion instead of coupling is more directly, intuitively, and easier. It narrows down what you have to focus on.

> Clearly, cohesion and coupling are interrelated. The greater the cohesion of individual modules in the system, the lower the coupling between modules will be. In actual practice, these two measures are correlated; that is, on the average, as one increases, the other decreases; but the correlation is not perfect. Maximizing the sum of module cohesion over all modules in a system should closely approximate the results one would obtain in trying to minimize coupling. However, it turns out to be easier both mathematically and practically to focus on cohesion.
>
> &mdash; &lt;&lt;Structured Design&gt;&gt; Larry Constantine


## Take a close look

There are 7 kinds of different cohesion as structured design described. the way of grouping file at MVC pattern, one of the most common cases of logical cohesion, haves inferior cohesion. 

Take Django as example: it groups *urls* together, and groups *views* together. you can mapping urls to views manually. On the surface, it looks more "pure" and neater, which is just a illusion, in fact, it's a worse form of organization and used frequently by many programmers.

~~~
                               +--------------------------------------------+
                               |  +--------+    +--------+    +--------+    |
                               |  |  url1  |    |  url2  |    |  url3  |    |  url components
    +--------+                 |  +--------+    +--------+    +--------+    |
    |  urls  |                 +--------------------------------------------+ 
    +--------+                                       |       
        |       ---------->                          | 
        v                                            v
    +--------+                 +--------------------------------------------+
    | views  |                 |  +--------+    +--------+    +--------+    |
    +--------+                 |  | view1  |    | view2  |    | view3  |    |  view components
                               |  +--------+    +--------+    +--------+    |
                               +--------------------------------------------+

~~~


Frankly, the structure looks pretty, clear, and each component is at right place. If you want to change *url*, you can quickly find out where it is, and if you want to change *view*, easily too.  Everything looks fine. However, if you go through it, you get to feel hard gradually.

The structure of files in Django app:

~~~
polls/
    ...
    urls.py
    views.py
~~~

If you modified *url1*, then you check it using some tools such as *git status*, finally you look some thing like this:

~~~
modified:   .../urls.py
~~~

and if you need to add a new url and corresponding function, you will see:

~~~
modified:   .../urls.py
modified:   .../views.py
~~~

Every time you have to modify two modules where there are only two modules.

Now, how can you tell from this what has changed? The answer is NO, you can't tell until you scan the detailed modification. From this, you can only know *url* changed or view changed or both, that's all, you can't specify which one it is, which function has been changed or added. So if you don't go through all of it, you can't ensure what has been effected ultimately, without doubt, it add uncertainty. In other words, you have to take the risk that modifying something might affect something else. The reality might be that due to carelessness, you introduce some new bug somewhere, while fixing a known bug. Why does this happens? Put all urls in one modules, just because they are similar, namely, they have same logic. But if we want higher cohesion, we should focus on interrelation between elements of the same module. We can see there is nearly no relationship between url components. Why should we group it all together in one module? And obviously, this is not the only problem you are going to face.

And we know *url1* will map request to *view1*, and the **relationship is necessary**. In Django, we can specify it explicitly. Imagine it: How would you handle this situation if the relationship is implicit, or in other words, you can't find out the relationship? It's hard to deal with, right?

> Note that the potential impact of changes has not been reduced by introducing these artificial boundaries between sections. These remain as complex and highly interconnected as before. The program as a whole may be marginally more complex, for the introduction of "module boundaries" introduces new elements into the system, and may require involved coding to accomplish the actual interfacing implied by so many intermodular references.  
> 
> &mdash; &lt;&lt;Structured Design&gt;&gt; Larry Constantine


~~~
       +--------------------------------------------+
       |  +--------+    +--------+    +--------+    |
       |  |  url1  |    |  url2  |    |  url3  |    |  url components
       |  +--------+    +--------+    +--------+    |
       +------|--------------|------------|---------+ 
              |              |            |          
              |              |            |          
              |              |            |         
       +------|--------------|------------|---------+
       |      v              v            v         |
       |  +--------+    +--------+    +--------+    |
       |  | view1  |    | view2  |    | view3  |    |  view components
       |  +--------+    +--------+    +--------+    |
       +--------------------------------------------+

~~~

Url module are tightly coupling to view module. I think this way may be a good measure: If a module is deleted, how many modules will be affected. Well known, if url module is deleted, then nothing can work.
(NOTE: I wonder if this structure is acceptable if there are dedicated people manage the two modules separately)

The above structure can be reorganized as following:

~~~

               /                  |                     \ 
              /                   |                      \
    +---------------+     +---------------+     +---------------+
    |   +------+    |     |   +------+    |     |   +------+    |
    |   | url1 |    |     |   | url2 |    |     |   | url3 |    |
    |   +------+    |     |   +------+    |     |   +------+    |
    |      |        |     |      |        |     |      |        |
    |      v        |     |      v        |     |      v        |
    |   +------+    |     |   +------+    |     |   +------+    |
    |   |view1 |    |     |   |view2 |    |     |   |view3 |    |
    |   +------+    |     |   +------+    |     |   +------+    |
    +---------------+     +---------------+     +---------------+
        module1               module2                module3

~~~

Now three modules have no coupling, and intramodule cohesion is higher than before. If you delete any module, it will not affect else two. Comparing previous one, this structure removes intermodular dependence and strengthen intramodule dependence, or it converts couping to cohesion.

> Adapting the system's design to the problem structure (or "application structure") is an extremely important design philosophy; we generally find that problematically related processing elements translate into highly interconnected code. Even if this were not true, structures that tend to group together highly interrelated elements (from the viewpoint of the problem.  once again) tend to be more effectively modular.  
> 
> &mdash; &lt;&lt;Structured Design&gt;&gt; Larry Constantine

Then, Flask takes another way. I think it's definitely an improvement. 

in this way:

~~~python
@app.route('/')
def hello():
    return 'Hello, World!'
~~~

So, Flask puts url and view together. Definitely, it make things more clear, and intuitive.

And, things are moving forward farther. In FastAPI, things become this:

~~~python
@app.post("/user/", response_model=UserOut)
def create_user(user: UserIn):
    return user
~~~

Obviously, it put more things together, and it gets better because putting relevant components together makes their relationship explicit. Seems that the structure become more "impure", but more pratical. There is a motto needs to be remembered:

> Practicality beats purity. 
> 
> &mdash; The Zen of Python, by Tim Peters

Those minds also embodied at everywhere of programming. For example, the statements of code:

> C++ Example of Bad Code That Jumps Around
>
>     MarketingData marketingData;
>     SalesData salesData;
>     TravelData travelData;
>   
>     travelData.ComputeQuarterly();
>     salesData.ComputeQuarterly();
>     marketingData.ComputeQuarterly();
>   
>     salesData.ComputeAnnual();
>     marketingData.ComputeAnnual();
>     travelData.ComputeAnnual();
>   
>     salesData.Print();
>     travelData.Print();
>     marketingData.Print();
>
> Suppose that you want to determine how marketingData is calculated. You have to start at the last line and track all references to marketingData back to the first line. marketingData is used in only a few other places, but you have to keep in mind how marketingData is used everywhere between the first and last references to it. In other words, you have to look at and think about every line of code in this fragment to figure out how marketingData is calculated. And of course this example is simpler than code you see in life-size systems.Here’s the same code with better organization:
>
>     MarketingData marketingData;
>     marketingData.ComputeQuarterly();
>     marketingData.ComputeAnnual();
>     marketingData.Print();
>
>     SalesData salesData;
>     salesData.ComputeQuarterly();
>     salesData.ComputeAnnual();
>     salesData.Print();
>
>     TravelData travelData;
>     travelData.ComputeQuarterly();
>     travelData.ComputeAnnual();
>     travelData.Print();
>
> &mdash; &lt;&lt;Code Complete&gt;&gt; Steve McConnell

Why the latter example is better than former example? After all, the former example is also organized by certain rule, it's, on the surface, not messy at all. If to explain it, I think the key point is relationship.

If use some lines to denote the implicit relationship, that would be like this:

~~~
                m MarketingData marketingData;                    
            s  /  SalesData salesData;                    
        t  /  /   TravelData travelData;                    
       /  /  /                                                                
       \  \  |                        
        t  \  \   travelData.ComputeQuarterly();                    
       /    s  \  salesData.ComputeQuarterly();                    
      /    /    m marketingData.ComputeQuarterly();                    
      |    \   /                                                               
      |     s  \  salesData.ComputeAnnual();                    
       \   /    m marketingData.ComputeAnnual();                    
        t  |   /  travelData.ComputeAnnual();                    
       /   \  /                                                                                
       \    s \   salesData.Print();                    
        t      \  travelData.Print();                    
                m marketingData.Print();                    
~~~

while maintaining it one day, you will find that in fact it's messy absolutely.
 
> Another possible contributor to complexity is the "span" of data elements - i.e., the number of program statements during which the status and value of a data element must be remembered by the programmer in order to comprehend what the module is doing; thus, a module is made more complex if a data element is loaded into an accumulator in the second instruction, and the data element then is not used until the 147th instruction.
>
> &mdash; &lt;&lt;Structured Design&gt;&gt; Larry Constantine

and the latter example would be like this:

~~~
  +-----------------------------------------+    
  |  |   MarketingData marketingData;       |
  |  |   marketingData.ComputeQuarterly();  |
  |  |   marketingData.ComputeAnnual();     |
  |  v   marketingData.Print();             |
  +-----------------------------------------+    
  +-----------------------------------------+    
  |  |   SalesData salesData;               |
  |  |   salesData.ComputeQuarterly();      |
  |  |   salesData.ComputeAnnual();         |
  |  v   salesData.Print();                 |
  +-----------------------------------------+    
  +-----------------------------------------+    
  |  |   TravelData travelData;             |
  |  |   travelData.ComputeQuarterly();     |
  |  |   travelData.ComputeAnnual();        |
  |  v   travelData.Print();                |
  +-----------------------------------------+    
~~~

Here, we just reorganize it in another way, then structure become much better. If looking carefully, you will find the relationship has not been reduced. Rather, it has been converted. (NOTE: here, I have some question: can the amount of relationships of a system be absolutely reduced or increased? Or the relationships can only by converted from one form to another through organizing structure ?)

## How to design




### TO BE CONTINUED ...

: *I realize that this is a long-term topic. I can only slowly complete it as my minds get deeper.*



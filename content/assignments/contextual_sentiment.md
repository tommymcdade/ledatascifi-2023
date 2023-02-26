## How to measure "contextual sentiment" in a 10-K

So you need to create variables describe the sentiment around a particular topic in a document.

This this most interesting when a firm has an overall positive  tone but speaks relatively negatively about a particular topic. Additionally, it can be interesting because you're specifically interested in that specific topic. 

Here's how I'd do it:

First, define the list of words that mean that topic is being discussed.

- To capture "Detroit Sports", you might include "Detroit Lions", "Red Wings" (specific enough "Detroit can be omitted), "Detroit Tigers", and "Detroit Pistons".
- Figuring out how many synonyms to add is hard. Too many and you'll start getting false positives (just "Detroit" or just "Lions" will capture discussions about other topics), too few and you'll miss discussions (false negatives). Getting topic lists right is hard work that involves lots of validation. (Propose a change, see if it works on example sentences, see if it changes anything in the larger dataset.)

Second, define regex patterns that will detect if that topic is being discussed positively.

- We talk about how to do this in class: Use `NEAR_regex`. We can use it to find anywhere one of our Detroit Sports words are near positive sentiment terms.
    - The most basic usage of the function is to give it a list of strings like `["topic1", "topic2"]` and it will design a regex that looks for `"topic1"` near `"topic2"`.
    - You can replace `"topic1"` with a _"list"_ of strings, and it will look for any of those strings near `"topic2"`.
    - How: Put a "|" between each term (`"|".join(list_of_words)`) and parentheses around the whole thing
    - Ex: `"(Detroit Lions|Red Wings|Detroit Tigers|Detroit Pistons)"`
- NEAR_regex will output a crazy looking regex pattern. Let's call it `detroit_sports_positive_regex`.

Third, check if that topic is being discussed positively.

1. _As if you were inside the loop you set up to go over the files you downloaded,_ pick just one firm, and open its 10-K file as a string variable.
1. Clean that string, because it contains lots of HTML tags and in general has lots of non-word characters.
    - See notes from class on how to do this and/or look through the textbook 
    - Only proceed when you have this working well. 
1. Use the regex pattern
   - `hits = len(re.findall(detroit_sports_positive_regex,<clean_10_text>))` will count the number of times the document discusses losing and save it to `hits`.
   - Save `hits` inside of the variable `detroit_sports_positive` for that document (put it in the correct row!)  
   - **Manually check it by opening the 10-K on the browser - do your functions give you the same values you'd create if you did it by hand?**

Fourth, repeat.

- Do that for the negative sentiment version: `detroit_sports_negative_regex`
- Do that for a second topic (positive and negative sentiment)
- Do that for a third topic (positive and negative sentiment)
- For each of those,
    - Open some 10-Ks manually and read them to verify if your guess for how to check actually results in hits.
    - If it doesn't work like you think (misses obvious discussions of the risk, or finds non-discussions), tweak it.

Finally, integrate this into your big for loop

1. Now, loop over all the firms, and for each firm, measure the risks in the corresponding 10-K. _Warning: Looping over rows in a pandas dataframe is a little different than normal! Look up how to do it!_
  
    **IMPORTANT! `.describe()` those variables after you're done!**
    - You should have observations for most/all firms for your new measures. **Fix it - do not proceed!**
    - If any of your variables are always 0, it's meaningless. **Change it - do not proceed!**
    - If any of your variables are always really high, consider if your search thinks too many things are that risk. Searching for "risk" for example is too vague. 
    
## Topics

- High level/MBA/Porter's 5: Sales, demand, competition, regulation, suppliers, customers, employees, investment, innovation
- Places: Ukraine, Taiwan, 
- Risks: antitrust; litigation - e.g. patent, consumer, class action; real estate; inflation; commodity; supply chain; natural disasters; weather; employees (fraud, compensation, departure); changes in tax policy; currency rates; regulatory approval; reputation; refinancing; 
- Prof. Kathleen Hanley [has a recent paper](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=2792943) on risks. It focuses on financial firms, which isn't our sample, but nevertheless, it contains a long list of risks in Table 5 you might find interesting.

```{tip}
This assignment is about covid. What factors of firms do you think might make a firm more or less resilient to operating in a pandemic?
```

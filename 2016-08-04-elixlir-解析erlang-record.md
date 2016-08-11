See question for answer. (This is at the top of the unanswered list)

Otherwise I found the answer during my investigations in a blog post on elixir-lang.org

For the most commonly used records:


```
defrecord :xmlElement, Record.extract(:xmlElement, from_lib: "xmerl/include/xmerl.hrl")
defrecord :xmlText, Record.extract(:xmlText, from_lib: "xmerl/include/xmerl.hrl")

```
stackoverflow ->

http://stackoverflow.com/questions/17345939/elixir-and-erlang-records-pattern-matching

# JavaScript Console snippets to improve Substack usability

To use these snippets, you need to [open](https://duckduckgo.com/?q=open+browser+console&ia=web) and use your [browser's console](https://duckduckgo.com/?q=using+browser+console+javascript&ia=web).

No need to be a JS expert, just try to enter the snippet into the console (your browser may not allow copy-paste for safety) and press Enter/Return to run it.

## Expand all comments

This snippet automatically clicks all the red buttons to expand more comments until there are none. It can take a long time. If the browser asks you if you want to kill it, don't, grant it more time.

```js
while(1){n=0;document.querySelectorAll('.collapsed-reply ').forEach((e)=>{e.click();n++});if(n===0)break}
```

## Suspend and resume loading more comments

On a busy comments page, the JS that loads more comments from the server into the web page takes a long time. And blocks typing etc. while it does that. And since it runs every 3 seconds it can be quite a problem.

Suspend loading new comments

```
HELP me write this snippet, please!
```

Resume loading new comments

```
HELP me write this snippet, please!
```

# What's up with Substack's front-end JavaScript app for comments?

TL/DR: Lots.

## It puts all visible comments into the DOM

A comments page with thousands of comments is lengthy. Rendering all of them at once is demanding of the browser and device. So don't do that. 
Put into the DOM only the stuff that would be visible to the user through the viewport scrolled to its current position.

How do you imagine apps like Google Photos allow me to scroll through 50 thousand pics collected over 20 years with a nice responsive UX?

Substack comments is in any case a single page app so why not?

## It polls for new comments every 3 seconds

Scenario: a page with thousands of comments. The user has for a while been browsing, reading, commenting, clicking 
the `Load More` and `N Replies` buttons to fill in the gaps. The DOM is now big and complex as described above.
So the browser is already working extra hard (CPU, battery, energy) to just display it all.

Then every three seconds it fetches from

    https://XXX.substack.com/api/v1/post/POSTID/comments?token=&all_comments=true&sort=best_first

and every response is a JSON doc of the same 246 comments. And processing every response overloads the browser and it 
freezes. This makes the afore mentioned browsing, reading, commenting, clicking difficult.

## The DOM is unnecesarily complex

Let's take a look at **one** public comment with **one** reply

```html
<table class="comment-content">
    <tr>
        <td class="comment-head">
            <div class="user-head ">
                <a href=""
                    ><div class="profile-img-wrap">
                        <img
                            src="https://cdn.substack.com/image/fetch/w_66,h_66,c_fill,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack.com%2Fimg%2Fanonymous-head.png"
                            width="0"
                            height="0"
                        /></div
                ></a>
                <div class="modal popup out gone  ">
                    <div class="modal-table">
                        <div class="modal-row">
                            <div class="modal-cell modal-content">
                                <div class="container">
                                    <button tabindex="0" class="button modal-btn modal-exit-btn " type="button">
                                        <svg
                                            role="img"
                                            width="15"
                                            height="15"
                                            viewBox="0 0 15 15"
                                            fill="none"
                                            stroke-width="1"
                                            stroke="#000"
                                            xmlns="http://www.w3.org/2000/svg"
                                        >
                                            <path d="M1 1L7.75 7.75M14.5 14.5L7.75 7.75M7.75 7.75L14.5 1L1 14.5"></path>
                                        </svg>
                                    </button>
                                    <div class="user-profile-modal">
                                        <div class="user-profile ">
                                            <div class="profile-image-wrapper">
                                                <img
                                                    class="user-profile-head"
                                                    src="https://cdn.substack.com/image/fetch/w_184,h_184,c_fill,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack.com%2Fimg%2Fanonymous-head.png"
                                                    width="92"
                                                    height="92"
                                                />
                                            </div>
                                            <div class="user-profile-name">RJ</div>
                                            <div class="user-profile-status">&nbsp;</div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </td>
        <td>
            <div class="comment-meta">
                <span class=""><a href="javascript:void(0)">RJ</a></span
                ><span><a href="/p/cult-nation-935/comments#comment-1057001" native="true">Jan 15</a></span>
            </div>
            <div class="comment-body">
                <p><span>Hey, I'm a subscriber but can't find the entire article. What's going on?</span></p>
            </div>
            <div class="comment-actions">
                <span
                    ><a href="javascript:void(0)" class="like-button"
                        ><svg
                            role="img"
                            width="15"
                            height="20"
                            viewBox="0 0 15 20"
                            fill="none"
                            stroke-width="1"
                            stroke="#000"
                            xmlns="http://www.w3.org/2000/svg"
                            class="animation"
                        >
                            <path
                                d="M1.73624 5.1145C2.43974 4.37137 3.37095 4 4.3036 4C5.23626 4 6.16745 4.37137 6.87097 5.1145L7.49949 5.77892L8.1227 5.11986C9.52973 3.63357 11.8557 3.6336 13.2627 5.11986C14.6698 6.60612 14.6698 8.98642 13.2627 10.4727C11.4639 12.3728 9.66583 14.2737 7.86703 16.1738C7.81927 16.2242 7.76183 16.2643 7.6982 16.2918C7.63456 16.3192 7.56606 16.3333 7.49683 16.3333C7.42761 16.3333 7.3591 16.3192 7.29547 16.2918C7.23184 16.2643 7.1744 16.2242 7.12664 16.1738L5.77904 14.7472L3.08384 11.8939L1.73624 10.4673C0.331003 8.98011 0.329213 6.60074 1.73624 5.1145Z"
                                stroke="#999999"
                            ></path></svg
                        ><svg
                            role="img"
                            width="15"
                            height="20"
                            viewBox="0 0 15 20"
                            fill="none"
                            stroke-width="1"
                            stroke="#000"
                            xmlns="http://www.w3.org/2000/svg"
                        >
                            <path
                                d="M1.73624 5.1145C2.43974 4.37137 3.37095 4 4.3036 4C5.23626 4 6.16745 4.37137 6.87097 5.1145L7.49949 5.77892L8.1227 5.11986C9.52973 3.63357 11.8557 3.6336 13.2627 5.11986C14.6698 6.60612 14.6698 8.98642 13.2627 10.4727C11.4639 12.3728 9.66583 14.2737 7.86703 16.1738C7.81927 16.2242 7.76183 16.2643 7.6982 16.2918C7.63456 16.3192 7.56606 16.3333 7.49683 16.3333C7.42761 16.3333 7.3591 16.3192 7.29547 16.2918C7.23184 16.2643 7.1744 16.2242 7.12664 16.1738L5.77904 14.7472L3.08384 11.8939L1.73624 10.4673C0.331003 8.98011 0.329213 6.60074 1.73624 5.1145Z"
                                stroke="#999999"
                            ></path></svg
                    ></a>
                    <div class="modal  out gone modal-free-signup ">
                        <div class="modal-table">
                            <div class="modal-row">
                                <div class="modal-cell modal-content">
                                    <div class="container">
                                        <button tabindex="0" class="button modal-btn modal-exit-btn " type="button">
                                            <svg
                                                role="img"
                                                width="15"
                                                height="15"
                                                viewBox="0 0 15 15"
                                                fill="none"
                                                stroke-width="1"
                                                stroke="#000"
                                                xmlns="http://www.w3.org/2000/svg"
                                            >
                                                <path
                                                    d="M1 1L7.75 7.75M14.5 14.5L7.75 7.75M7.75 7.75L14.5 1L1 14.5"
                                                ></path>
                                            </svg>
                                        </button>
                                        <h2 class="page-title">Sign up to like comment</h2>
                                        <form class="form " action="/api/v1/free?nojs=true" method="post" novalidate="">
                                            <input
                                                type="hidden"
                                                name="first_url"
                                                value="https://taibbi.substack.com/"
                                            /><input
                                                type="hidden"
                                                name="first_referrer"
                                                value="https://taibbi.substack.com/account/login?redirect=%2F&amp;email=&amp;with_password="
                                            /><input
                                                type="hidden"
                                                name="current_url"
                                                value="https://taibbi.substack.com/p/cult-nation-935/comments"
                                            /><input
                                                type="hidden"
                                                name="current_referrer"
                                                value="https://taibbi.substack.com/p/cult-nation-935"
                                            /><input type="hidden" name="referral_code" /><input
                                                type="hidden"
                                                name="source"
                                                value="like_button"
                                            />
                                            <div class="sideBySideWrap">
                                                <input
                                                    type="email"
                                                    name="fake_email"
                                                    placeholder="email"
                                                    style="position: absolute; top: -10000px; left: -10000px;"
                                                /><input
                                                    type="password"
                                                    name="fake_password"
                                                    placeholder="password"
                                                    style="position: absolute; top: -10000px; left: -10000px;"
                                                /><input
                                                    type="email"
                                                    name="email"
                                                    readonly=""
                                                    placeholder="Type your email…"
                                                /><button
                                                    tabindex="0"
                                                    class="button rightButton primary subscribe-btn "
                                                    type="submit"
                                                >
                                                    <b>Subscribe</b>
                                                </button>
                                            </div>
                                            <div id="error-container"></div>
                                            <div class="subtle-help-text below-input">
                                                <a class="login-button" href="/logout" native="true">Not you?</a
                                                ><a class="legal" href="/privacy" target="_blank" native="true"
                                                    >Privacy</a
                                                ><a class="legal" href="/tos" target="_blank" native="true">Terms</a
                                                ><a
                                                    class="legal"
                                                    href="https://substack.com/ccpa#personal-data-collected"
                                                    target="_blank"
                                                    native="true"
                                                    >Information collection notice</a
                                                >
                                            </div>
                                        </form>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div></span
                ><span><a href="javascript:void(0)">Reply</a></span>
            </div>
        </td>
    </tr>
</table>
```

This is repeated thousands of times.

I'm not sure I'm up to the task of explaining *all* the problems here but...

- Why all the embedded SVG documents? It's ok to paste the same SVG a handful of times but not tens or hundreds, let alone thousands.
- Why all the forms? The user (and therefore DOM) doesn't need a reply comment form until someone clicks a `Reply` button. Insert the form then!
- `<a href="javascript:void(0)">` `<sigh />` really?
- Why all the `style` attributes, like `style="position: absolute; top: -10000px; left: -10000px;"`?

Just optimizing this stuff, which would be a few hours work and a few more testing, might make a big improvment.


## That adds up

[Here is a public Substack page with 334 comments](https://greenwald.substack.com/p/violence-in-the-capitol-dangers-in-bbe/comments) (at the time I exported it). [Here's the DOM it generates](https://fsbdoc.s3.amazonaws.com/subparstack-comments.html) exported as HTML. It has content-type `text/plain` to make it easier for your browser to display. Even so mine is having a hard time.


## XHR activity

From the network acticity console we see the comments app polling for new comments to display

![Network view of XHR](https://monosnap.com/image/ZZHrYjzsLThm8cuFjA8nFRUQ6aSP81)

This raises a few questions:

- Why return 100 KiB of data every three seconds even when there are no changes to the comments since the last poll?

- Why return these particular comments?

- Why the same comments every time?

- Why not return only changes since the last poll?

The timing of these requests isn't really a problem for the user but suggests a design that's wasteful of resources.  Most database
performance problems can be solved by throwing money at them (hardware and energy) but some are easily optimized with a
change in the code. I suspect there are big gains to be made there but that's not our focus in looking at the webapp.

This is the problem for us end users

![initial waterfall](https://monosnap.com/image/fSUkLMTr7ZUPnFwztYmjtzZpFbyVix)

That's the response on initial display of the comments section.

Then I expand the comments (clicking `Load More` and `N Replies` buttons, takes a long time, and several clicks of the `Wait` button when Firefox displays  
![Slow web page](https://monosnap.com/image/DsKcctkHeAavSKVY8MudMnMP6fCrZw)

Now here's the waterfall timing while doing nothing, just letting the XHR timer do its thing.

![XHR waterfall](https://monosnap.com/image/b0QVSHj9qD9Xy1l1Evzcvr0t5YXm4O)

Poor browser doesn't stand a chance, does it? It's completely frozen for more than half the time.

Btw: if your device is running off a battery, don't leave this browser window open.


# Summary

I only just got started and I can see a few very simple things that could be changed that might suffice to make this SPA perform acceptably.

Really, I don't think this is my job. I pay to *use* Substack comments.

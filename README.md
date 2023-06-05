# oCSS
 Omnis CSS 2.0

# With CSS in HTML, you can change the CSS file and give your web pages an entirely different look. oCSS (Omnis CSS) is an object that will do the same thing for your Omnis application simply by instantiating it in your window superclass.

Originally, I instantiated the object in the superclass for each window, which resulted in many instances of oCSS that only were needed when the window first opened. Now it is instantiated as a task variable as an object reference so that it exists only once and can be called from anywhere.


In the $construct of your window superclass, include the following code:
If isclear(torCss)
Do $clib.$objects.oCSS.$newref($cinst().$ref) Returns torCss ## torCss is a object reference task variable
End If
Do torCss.$initSkin($cinst().$ref)

The included sample windows, wCSS & wCSS2, include fields for each type of window object (wCSS) and External Component (wCSS2) to demonstrate its use. In oCSS, there is a method for each type of field. Within the method for each type, you can alter the properties for that type of field throughout the application.

## What is the use of Omnis CSS?

Many of the Omnis Studio applications I have seen have a fairly plain look. My own applications have had the same problem. There are some reasons why I think this may be.
- Omnis developers have often not looked at other types of applications, especially web applications for inspiration.
- The variety of the built-in components in Omnis is limited and getting long in the tooth.
- Even though Themes cover many of the properties, they cannot be used to set all of them, and some have unintended consequences. For example, setting the color for push buttons, also sets the same color for dropdown menus and a few other things.
- If you want to change the look and feel of your application, it can be a daunting task to find a way to consistently update the look of all the windows short of changing every window and hoping you didn't miss anything.

When I wrote my DonorWorks application for non-profits over 25 years ago, I did what most Omnis developers did and made gray windows with black and blue text and used red for highlighting. Many applications do the same thing today, maybe adding a splash of color or a custom icon here and there to create some variety.

DonorWorks went through a short stretch where after doing a demo for a customer, we would contact them a few days later to see if they were ready to purchase. Over a period of a few months we were told that it just didn't seem very user-friendly compared to other software they had looked at. We tried to find out what they meant and they couldn't really pin it down. I asked my wife about it and she said "Your application looks very masculine, whereas most of the users of your software are women." I said, "What do you mean? It's very clean and with nice contrast." She said, "Exactly!" So over the weekend we revamped the whole application.

We put together several sets of colors for buttons, lists and windows and let the user select between them. I made this work by setting these in the base window class and doing a $sendall to all the objects on the window of that type. This worked well, and then each time we did a demo, we showed the potential clients how they could change the colors of the application at any time. Surprisingly they were very excited about it and we rarely after that had a prospect tell us it was not user-friendly. And we had some customers actually tell us they looked forward to Monday morning when they would change their color scheme for the week.

Recently I was trying to determine how we could provide a visual makeover for a client who would not likely approve much money for this. So I remembered back to what I had done in DonorWorks and wondered how I could generalize the work to work with any application that maintained one or more base windows. I had also done a number of websites and had played around with using different CSS files to try out different looks for the site.

Omnis CSS uses the same idea as CSS in HTML to have all the visual aspects of the application be isolated into a single location. Any changes there adjust the visual portions of the rest of the application.

Like CSS, you can override settings for particular objects. For example, if you want a field label to indicate the field is required, put "kRequired" in the $userinfo property for the label. The $label method sees this and makes the label kRed. You can see a number of overridden items throughout the code. If you want to make a particular field ignore the standard properties, use "kCustom".
You can have multiple settings in the $userinfo field. Just have some kind of separation between them, like a comma, and keep them unique so one doesn't get included within another ('kGrid', 'kGridLine').
In a complex grid, fields may display differently than they would directly on the window. For example, on the window you may want to have a thin line around the field, but in a complex grid that is not necessary because the dividers between columns provide the same thing. This is considered here, but you can change it.
Notice that we've also decided that spell-checking should be turned on for multi-line entry fields (available in Studio 11). By including that setting in the $multilineentry method, all fields of that type get the property and you didn't have to go through and update the property on all the fields in your application.

oCSS starts by putting together a list of all the objects on a window, including recursively looping through container classes, like scrollboxes, complex grids, etc. That does not include subwindows, as they will have their own oCSS applied to them. Then each of those objects has their own CSS-like properties assigned to them on-the-fly.

oCSS was intended originally to work on-the-fly as the window opened. For straightforward windows, this is quite fast. However, for very complex windows, it can slow things down. So there is an option to "compile" the window.
To compile a window, override the variables "ibIsCompiled" and "ibShouldCompile", override the "$isCompiled" method in the window and put in the code commented out in wSuper.$isCompiled. (I was having trouble getting the code from the superclass to run using the variables in the child class, so this was my compromised. Let me know of you find out how I can just override the child instance of the variable.)
Set the initial value for "ibIsCompiled" to kFalse and "ibShouldCompile" to kTrue.
When you open the window for the first time, it will both run the oCSS code as the window is opening and also save all the changes made in the instance back to the library. Then it sets "ibIsCompiled" to kTrue. After that, it does not run the oCSS code, but just opens what is saved on disk. It also sets "ibShouldCompile" to kFalse so that it won't try to compile it each time.
Because it is both "compiling" and saving at the same time, sometimes you need to open and close the window a couple times for everything to take effect and be saved.

I've set some odd properties on some items, like the background and the push button just so you can see that it is actually doing something.

## Future potential enhancements
- Studio 11 introduces a new options for $makelist to recursively obtain all objects on a window regardless of container level. This uses the constant kRecursive as the first parameter is $makelist and others. This has not yet been implemented.
- Provide a way to read in a set of properties from a file or database record. This would allow for changing the look and feel on the fly, or allowing users to set up their own colors and themes. This is pretty easy to do. Just read from some file or record and set the instance variables accordingly.
- Originally, this project was intended to always skin windows on the fly. However, with adding the ability to "compile" windows for quicker opening, this causes issues with changing the look and feel on the fly. I'm thinking through ways to make some global setting that would track when the look and feel was last change and automatically recompile windows as they open when the colors, etc. are changed.
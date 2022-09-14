Tutorial located here:

https://www.youtube.com/watch?v=jfMHA8SqUL4&t=81s

Table of Contents
 [compiling sass](#1:17)
 [partials](#12:49) 
 [variables](#26:17) 
 [BEM](#50:00)
 [layout](#57:08)
 [setting widths](#1:18:10)
 [sass mixins](#1:36:08)
    [breakpoints](#breakpoints)
[responsive typography](#2:06:35)
[sass functions](#2:33:24)
# 1:17 
compiling-sass: 
    I have the correct extension. Kevin has an entirely different approach to configuring our sass environment. There is also a "install sass globally" option. Those last two wouldn't work; Jessica's method here seems to be acceptable to Kevin and others. 

    I learned about the user/settings.json file. our sass configuration has a spot in that, right now that's what is sending all the generated CSS to the 'util' folder. (i don't like that name... even if it is industry standard, I would prefer to name it 'generated-css'.)

# 12:49 
Partials
    Jessica is very intent on lots of folders with some extra abstraction here, file folders for single files and one other file just to give the entire folder relevance('_index.scss')
        -Interestingly enough many of these folder names give the folders a visual spark. 

    a word of connection is '@forward'. However, the file of the desired styles doesn't forward itself, the way to do it is have :
    "@forward 'partial_name'" in the _index.scss file in that folder. 

    Anything scss file that does lead with an underscore will compile to a css file of the same name.

    Our 'style.scss' file is our one scss file that will become css. it is set to compile to our 'style.css' inside the dist folder. Inside the 'style.scss' file are the @forward of all the funky folder names of what's rules will be compiled to css. It is necessary for these folders to contain a '_index.scss' file. 

    @use is for when you need to include a partial inside a partial that is not _index. Jessica puts our fonts in a 'util' folder and needs to access them in our boilerplate/reset, @use was used in that case. 

  # 26:17 
Sass variables and custom properties

    Sass variables start with $, and don't need to be set inside of a block. 

    css properties basically are variables, they are defined with --varname and called with var(--varname). 

    css properties are newer, one thing that makes them better is their ability to be manipulated by javascript. 

 # 43:45
    Commence building demo website 

 # 50:00 
    Sass and BEM 
    BEM is a naming convention that is supposed to make things easier. More information can be found here: 
    https://andrew-barnes.medium.com/bem-and-sass-a-perfect-match-5e48d9bc3894

# 57:08 
Mobile layout 
    we are building the mobile style first and using the media query to alter the settings for bigger screens.

# 1:04:30 Desktop Layout
    Using display: grid for both mobile and desktop, even though display: flex would suffice. I like it. 

    ponder this code here:

    .grid {
        display: grid;
        grid-template-columns: 1fr;
        grid-template-rows: auto auto;
        gap: 40px; 

        @media (min-width:900px) {
            grid-template-columns: 3fr 2fr;
            grid-template-rows: auto;       
        }
    }
    each argument for grid-template-* is the settings for each row or column. Notice how with two items, we can easily set either configuration with grid 

        --auto auto is because we know there are going to be two items on the cross axis! we could set this to fr values but (unlike flex-box) it wont adjust for the inner content. 

    By using the 'min-width' media query she is doing the mobile design first. Because mobile design tends to be simpler; so do the mobile design as a base and then complicate it with a 'min-width'. 

    Strange though, that I believe Kevin said he was using a mobile first approach first? maybe not, because he sets his media query up with max width. Maybe its that he does the stuff for mobile in the 'max-width' first. 

    but doesn't it make sense to make your root thing right and then introduce the media query on top of that?

    whichever approach, you can always include the opposite limit to make rules exclusive to the size you aren't starting with, best practice is to use the same breakpoint you are already using.

# 1:18:10 - 
setting widths
    setting width on outermost container not good, we should set a max-width instead. 
        -Even better, set the width with a min function! 
        like this!  width: min(100%-40px, 1000px);

        -and then use 'margin-inline: auto' to equify the side margins. 


## Breakpoints 
        -can set to variables like small, medium, large, to not have to remember the value. Jessica puts these in a sassy map. 
        -She always makes a map for "breakpoints-down" for any max-widths she might need. For whatever odd reason, rather then match the exact value of "breakpoints-up", she subtracts .02 pixels for every value
            the .02 difference is whats needed for changes to happen at the exact same time. 
    -em is the best unit here for browser compatibility. 


# 1:36:08
Sass Mixins 
    Mixins are just functions.. reusable bits of code. 
    
        @mixin breakpoint($size) {
            @media (min-width: map-get($breakpoints-up, $size)){
                @content; 
            }
        }
    The map is structured like this:

        $breakpoints-up: (
            "medium": 43.75em, 
            "large": 56.25em, 
            "xlarge": 90em, 
        );
    notice the lack of dot notation when accessing a map key. 
    Also notice the @content. 

    In order to access this function from a different partial, I have to use '@include'

    ~the cascade still applies in scss - important to know for media queries. 
        ~for min width constraint from top to bottom, go small to large. 
        ~for the max-width, do the opposite. 
        ~I believe that having any breakpoint initofitself increases the specificity of the rule. 


# 2:06:35 
    Responsive Typography

    this is delicious:
        h1{
        font-size: calc(16px + 2vw);
        }

    vw is view-width, if 'responsive' means adjusting to device widths, its unit is relative to that. However, straight view width makes for to drastic a font size. The calc function is a nice way to alleviate that. 

    this is even better: 
        h1 {
            font-size: clamp(28px, 16px + 2vw, 40px);
        }
        NEW SHORTCUT UNLOCKED - shift option [down or up] duplicates the line. 

    but I really should do this: 
        h1 {
            font-size: clamp(1.75px, 1rem + 2vw, 2.5rem);
        }   

    if the user has their base font size changed, this is affected.
    its smart to always use vw units with rem. always have the rem. 

    rem bases itself off of the user preferred font size. 

    em is not related to this. em is related to its parent element. so it is inherited. 

    ..so they are completely different. 

# 2:33:24
    #FUNCTIONS#
    here is a handy function to convert any px to rem units, including a catch for invalid units. 

@function rem($pixel) {
    @if math.is-unitless($pixel){
        @return math.div($pixel, 16) + rem;
    }  
    @else {
        @error "don't use units when using the rem function";
    }
}


Jessica then replaces all usage of pixels to rem. This doesn't make sense to me, because isn't rem dependant on user preference for fonts? if, say, our padding is in rem, then a user with a larger font size preference looked at website, wouldn't that increase all of our padding?

She then does a similar function with em. I'll consider getting at this functionality as I go forward, it seems like a do once and copy and paste over and over again type thing. 

WHY JESSICA USES 'EM'

EM relates to the parent. Although for elements inside the body, em will compound if used more than once in one lineage.

However, the @media will never have a parent other then the html, so 1em is always 16 px. But I guess px doesn't work the same across browsers(?), and we should always be using em for media queries. 

What you're changing when you change that is the display font size, not the browser's inherent font size.

# I still don't understand why use rem for padding and shit. 

# i shall git repos here just because. 

# 3:03:39


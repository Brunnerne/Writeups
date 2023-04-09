# forget-me-not (misc)

The website has a "Forgot password" button, leading us to the three questions we needed to answer:

1. Business email
2. First car (make and model)
3. Favourite restaurant

## Business Email

Lots of OSINT - we used `sherlock` on the known username `CryptoGeniusL1` to get the initial accounts.
Here we find a GitHub and a Twitter:

https://www.github.com/CryptoGeniusL1

https://twitter.com/CryptoGeniusL1

On the GitHub, we find a link to his workplace: https://eternalzenith.cc/. On the "about us" page we see the emails of the founders, and there is a clear format: `lastname DOT initial of firstname AT eternalzenith.com`. We still needs his name and continue to his Twitter.

Here, he mentions his new car and has a conversation with an old friend interested in buying his old (first) car. We are told it is at his parents' place and is clearly visible from the street. He also mentions his Mastodon profile: https://techhub.social/@CryptoGeniusL1 which gives us his full name and his personal email, together with him talking about his Facebook.

His full name is Lewis D. Striegel and so we guess his business email is `striegel.l@eternalzenith.com`, which the website confirms is correct.

## First car

We look for his full name on Facebook and find his profile: https://www.facebook.com/profile.php?id=100088890721847.

He is friends with his dad: https://www.facebook.com/profile.php?id=100088553535119 who has his flickr account linked on there: https://www.flickr.com/photos/197144535@N04/.

On his flickr, he has taken multiple photos of his computer screen with his phone. The final image is one directly from the computer with a text mentioning his son teaching him this for privacy reason. This points towards privacy issues with the first images, and flick is known for not stripping metadata - so we download a few images and see they still have geolocation on: 41°32'42.5"N 93°45'34.1"W.

We go here on Google Maps street view and find a house in the correct city (Des Moines, Iowa) with two cars in the driveway. One is black and is very likely the one mentioned on Twitter. It's difficult to make out the exact model, but using Google Lens, we see it is a Chevrolet Malibu. This answer is, however, incorrect, but after some searching we find it to be a 2013 model, and `Chevrolet Malibu 2013` is accepted.

## Restaurant

On his Twitter, we find the following tweet: `Finally got around to trying out that new sushi restaurant and it was absolutely amazing. Highly recommend it to anyone in the area. (You know where to find my Yelp if you know me lol)`

So we look for his Yelp, which we can find based on his email: https://www.yelp.com/user_details?userid=5mTVlAAMPgRgQaynwg0ywA. He has made 4 reviews and one is a 5-star review of a restaurant, he had "fond memories of from when he was a kid", namely `B-Bop's`. This is accepted as the third and final answer, giving us access to his account and the flag.

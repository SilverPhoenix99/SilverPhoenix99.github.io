---
title:  "Matlab Vector Graphics"
date:   2009-06-10 18:01:00 GMT
tags:   colormap eps matlab plot scatter "vector graphics"
---
Matlab v7.5 still has trouble with vector graphics. When trying to create a vector image it, instead, generates a bitmap image if you use rgb colors (for example in [scatter](https://mathworks.com/help/matlab/ref/scatter.html)) instead of colormap indices.

Here's an example:

![clusters graph](http://4.bp.blogspot.com/_4hHqfZFIq3A/SjNx76zD8eI/AAAAAAAAAAM/6ms_PzFZMuo/s320/clusters.png)

But there is a workaround. Instead of doing this:

```matlab
scatter(data(:,1), data(:,2), size, rgb, 'filled');
```

Do it like this to be able to export in vector format (like eps):

```matlab
colormap(rgb);
n = size(rgb, 1);
indices = cumsum( ones(n,1) );
scatter(data(:,1), data(:,2), size, indices, 'filled');
```

Now each point, instead of being colored by the rgb data, it is indexed to the custom colormap, which is exactly made up of the rgb data we want.

Now it's possible to export to svg.

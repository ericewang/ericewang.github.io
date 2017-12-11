---
layout: default
title: Testing code blocks
#date: 2017-12-17 12:00:00 -0800
excerpt_separator: <!--more-->
---

## Testing code blocks

{% highlight ruby %}
def show
  @widget = Widget(params[:id])
  respond_to do |format|
    format.html # show.html.erb
    format.json { render json: @widget }
  end
end
{% endhighlight %}

I can't wait to do more with this!
<!--more-->


# Pattern Placeholders

In your URL pattern you can also use the same `:` syntax to denote a variable position holder. These position holders are **alpha-numeric** by default:

`addRoute( pattern="blog/:year/:month?/:day?" );`

Once a URL is matched to the route, those placeholders will become request collection variables:

`http://localhost/blog/2012/12/22 -> rc.year=2012, rc.month=12, rc.day=22`


# Project-4 - Folio <img src='https://i.imgur.com/4Jx5UcI.png' width="80"/>

<h1>Overview</h1>

The Brief for this project was to create a Flask + React Application, utilising the information we had been taught in the week prior. After a successful project 3, I decided to work with 2 of the same students I had worked with on project 3 for this project. We decided on the ambitious concept of creating a Stock & Crypto trading website, allowing users to invest $100,000 of fake money and track their investments.  

-----
<h2>Brief</h2>

* **Build a full-stack application** by making your own backend and your own front-end
* **Use a Python Flask API** using a Flask REST Framework to serve your data from a Postgres database
* **Consume your API with a separate front-end** built with React
* **Be a complete product** which most likely means multiple relationships and CRUD functionality for at least a couple of models
* **Implement thoughtful user stories/wireframes** that are significant enough to help you know which features are core MVP and which you can cut
* **Have a visually impressive design**

----

<h2>Approach</h2>

<h4>Planning and Setup</h4>

As with my previous project, we ensured that we had a fully planned out idea prior to beginning any coding for this project, believing that this would save us time down the line. The planning stage consisted of discussions about how both the front and back-end would be structured. Although different technologies were being used, a similar structured approach to the initial setup seemed like the best way forward. This went smoothly for the most part, however due to being less familiar with SQLAlchemy and Flask, it did take longer than had initially planned.

<img src='https://i.imgur.com/NCGclMI.png'/>

<h4>Back-End</h4>

For this project, we switched Mongo DB & Mongoose for Flask and SQLAlchemy. In spite of this, the MVC structure was still able to be implemented. We created Models for the asset and the user, along with a model for 'trades'. When an asset is  bought / sold, a trade object is generated that records all  of the transaction information:

```
class Trade(db.Model, BaseModel):

    __tablename__ = "trades"

    name_of_asset = db.Column(db.String(100), nullable=False)
    asset_price = db.Column(db.Float, nullable=False)
    qty_purchased = db.Column(db.Float, nullable=False)
    total_trade_value = db.Column(db.Float, nullable=False)
    transaction_type = db.Column(db.String(10), nullable=False)
    asset_type = db.Column(db.String(10), nullable=False)
    user_id = db.Column(db.Integer, db.ForeignKey("users.id", ondelete="CASCADE"))
    stock_id = db.Column(db.Integer, db.ForeignKey("stocks.id", ondelete="CASCADE")
```

We used a join table to allow for a front-end 'favourite' functionality, and created controllers to allow all of the front-end functionality that we had planned. Marshmallow was also utilised in order to  Marshmallow for serialisation. 


<h4>Front-End</h4>

Having used Bulma as the CSS framework for the previous two projects, I felt it important to attempt to use a different framework for this project. We decided on using React Semantic UI, which proved to be a learning experience! The team wanted a clean looking website that reflected the financial industry, which resulted in a simple colour scheme with lots of darker tones. 

One issue I came across early was attempting to implement a sidebar rather than the traditional navbar along the top of the page. This turned out to not be possible without implementing a wide variety of changes to code that had already been written, and as such we ended up going with a more traditional navbar along the top. 

I was tasked with creating the Portfolio page, which would show the user the stocks/crypto they hold, along with a list of all of their transactions and their favourites. This page proved to be by far the most time consuming aspect of the website. For the required information to be shown, all trades for the user needed to be compiled, with calculations being made to determine how many of each stock and crypto that user holds. This information would then need to be used in conjunction with information pulled form an external API which provided the current stock and crypto prices. 

To get this working I had to create two functions, one for stocks and one for crypto. Below is the crypto version:

```
  useEffect(() => {
    const helperFunctionCrypto = async () => {
      const crypto = filterCrypto()
      const cryptoNames = [...new Set(crypto.map((crypto) => crypto.name_of_asset))]

      const cryptoPrices = await Promise.all(cryptoNames.map(async (cryptoName) => ({ name: cryptoName, price: await getCryptoPrice(cryptoName) })))
      const groupedTrades = filterCrypto().reduce((acc, trade) => {
        const existingObject = acc.find(obj => obj.name === trade.name_of_asset)
        if (existingObject) {
          existingObject.stocksHeld = existingObject.stocksHeld + trade.qty_purchased
          existingObject.pricePaid = existingObject.pricePaid + trade.total_trade_value
          existingObject.currentValue = existingObject.stocksHeld * (cryptoPrices.find(price => price.name === trade.name_of_asset).price) 
          return acc
        } else {
          return [...acc, {
            name: trade.name_of_asset,
            stocksHeld: trade.qty_purchased,
            pricePaid: trade.total_trade_value,
            currentValue: (cryptoPrices.find(price => price.name === trade.name_of_asset).price) * trade.qty_purchased
          }]
        }
      }, [])
      updateYourCrypto(groupedTrades)
    }
    helperFunctionCrypto()
  }, [tradeData])
```

All of the pages were linked as below:

```
const App = () => (
  <BrowserRouter>
    <Sidebar />
    <Switch>
      <Route exact path="/" component={Home} />
      <Route exact path="/register" component={Register} />
      <Route exact path="/login" component={Login} />
      <Route exact path="/portfolio" component={Portfolio} />
      <Route exact path="/research" component={Research} />
      <Route exact path="/trading" component={Trading} />
      <Route exact path="/asset/:assetId" component={Asset} />
      <Route exact path="/about" component={About} />
    </Switch>
    <Footer />
  </BrowserRouter>
)
```

<h4>Challenges</h4>

* One of the main challenges throughout this project was the sheer amount of information being pulled from the 2 main APIs that we used for stocks and Crypto. Splitting up the two proved difficult, and this could have been avoided with further planning and perhaps not taking on as much of a challenge!
* The issues we faced as a result of the limited back-end resulted in not being able to implement some of the features we had initially talked about. This also ate into the time we had for the overall styling of the website. 

<h4>Successes</h4>

* We achieved the brief of creating a full stack application utilising the required technologies, and hitting a number of the ambitious targets that we had initially set ourselves. 
* While using a new CSS Framework was tricky at times, it gave all of us a better understanding of the benefits of different frameworks
    

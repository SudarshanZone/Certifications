Sure, here's how you can use GORM to fetch data from the Upstox API and store it in a PostgreSQL database.

1. **Install dependencies**: Ensure you have the required packages by running:
    ```sh
    go get -u gorm.io/gorm
    go get -u gorm.io/driver/postgres
    ```

2. **Code**:
    ```go
    package main

    import (
        "encoding/json"
        "fmt"
        "io/ioutil"
        "net/http"

        "gorm.io/driver/postgres"
        "gorm.io/gorm"
    )

    type Position struct {
        gorm.Model
        Exchange            string  `json:"exchange"`
        Multiplier          float64 `json:"multiplier"`
        Value               float64 `json:"value"`
        PnL                 float64 `json:"pnl"`
        Product             string  `json:"product"`
        InstrumentToken     string  `json:"instrument_token"`
        AveragePrice        float64 `json:"average_price"`
        BuyValue            float64 `json:"buy_value"`
        OvernightQuantity   int32   `json:"overnight_quantity"`
        DayBuyValue         float64 `json:"day_buy_value"`
        DayBuyPrice         float64 `json:"day_buy_price"`
        OvernightBuyAmount  float64 `json:"overnight_buy_amount"`
        OvernightBuyQuantity int32  `json:"overnight_buy_quantity"`
        DayBuyQuantity      int32   `json:"day_buy_quantity"`
        DaySellValue        float64 `json:"day_sell_value"`
        DaySellPrice        float64 `json:"day_sell_price"`
        OvernightSellAmount float64 `json:"overnight_sell_amount"`
        OvernightSellQuantity int32 `json:"overnight_sell_quantity"`
        DaySellQuantity     int32   `json:"day_sell_quantity"`
        Quantity            int32   `json:"quantity"`
        LastPrice           float64 `json:"last_price"`
        Unrealised          float64 `json:"unrealised"`
        Realised            float64 `json:"realised"`
        SellValue           float64 `json:"sell_value"`
        TradingSymbol       string  `json:"trading_symbol"`
        ClosePrice          float64 `json:"close_price"`
        BuyPrice            float64 `json:"buy_price"`
        SellPrice           float64 `json:"sell_price"`
    }

    type Response struct {
        Status string     `json:"status"`
        Data   []Position `json:"data"`
    }

    func main() {
        // Replace with your actual access token
        accessToken := "your_access_token_here"
        
        // URL for the API endpoint
        url := "https://api.upstox.com/v2/portfolio/short-term-positions"

        // Create a new request
        req, err := http.NewRequest("GET", url, nil)
        if err != nil {
            fmt.Println("Error creating request:", err)
            return
        }

        // Add required headers
        req.Header.Add("Authorization", "Bearer "+accessToken)
        req.Header.Add("Accept", "application/json")

        // Make the request
        client := &http.Client{}
        resp, err := client.Do(req)
        if err != nil {
            fmt.Println("Error making request:", err)
            return
        }
        defer resp.Body.Close()

        // Read the response body
        body, err := ioutil.ReadAll(resp.Body)
        if err != nil {
            fmt.Println("Error reading response body:", err)
            return
        }

        // Parse the JSON response
        var response Response
        err = json.Unmarshal(body, &response)
        if err != nil {
            fmt.Println("Error parsing JSON response:", err)
            return
        }

        // Database connection
        dsn := "user=yourusername dbname=yourdbname sslmode=disable password=yourpassword"
        db, err := gorm.Open(postgres.Open(dsn), &gorm.Config{})
        if err != nil {
            fmt.Println("Error connecting to the database:", err)
            return
        }

        // Migrate the schema
        db.AutoMigrate(&Position{})

        // Store the data in the database
        for _, position := range response.Data {
            db.Create(&position)
        }

        fmt.Println("Data fetched and stored successfully")
    }
    ```

Replace the following placeholders with actual values:

- `"your_access_token_here"`: Your Upstox API access token.
- `"user=yourusername dbname=yourdbname sslmode=disable password=yourpassword"`: Your PostgreSQL connection string.

This code uses GORM to handle database interactions. It automatically creates the `positions` table if it doesn't exist and inserts the fetched data into the table.

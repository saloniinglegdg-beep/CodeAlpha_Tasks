# SECTION 1: HARDCODED STOCK PRICES - Dictionary of stock prices
class StockPrices:
    STOCK_PRICES = {
        "AAPL": 180.50,
        "TSLA": 250.75, 
        "GOOGL": 2780.25,
        "MSFT": 425.30,
        "AMZN": 3520.80,
        "NVDA": 890.45
    }
    
    @staticmethod
    def get_price(symbol):
        symbol = symbol.upper()
        return StockPrices.STOCK_PRICES.get(symbol, 0.0)

# SECTION 2: USER INPUT PROCESSING - Stock name + quantity
class PortfolioInput:
    @staticmethod
    def get_stock_input():
        """Get stock symbol and quantity from user"""
        portfolio = {}
        
        print("📈 STOCK PORTFOLIO TRACKER")
        print("Enter stocks (symbol + quantity) or 'done' to finish:")
        
        while True:
            symbol = input("\nStock symbol (e.g., AAPL): ").strip().upper()
            
            if symbol.lower() == 'done':
                break
                
            if symbol not in StockPrices.STOCK_PRICES:
                print(f" '{symbol}' not found. Available: {', '.join(StockPrices.STOCK_PRICES.keys())}")
                continue
            
            try:
                quantity = float(input(f"Quantity for {symbol}: "))
                if quantity <= 0:
                    print(" Quantity must be positive!")
                    continue
                portfolio[symbol] = quantity
                print(f"✅ Added {quantity} shares of {symbol}")
            except ValueError:
                print(" Enter valid number for quantity!")
        
        return portfolio

# SECTION 3: INVESTMENT CALCULATION - Total value computation
class InvestmentCalculator:
    @staticmethod
    def calculate_portfolio_value(portfolio):
        """Calculate total portfolio value"""
        total_value = 0.0
        stock_details = []
        
        print("\n PORTFOLIO SUMMARY")
        print("=" * 50)
        print(f"{'Stock':<8} {'Qty':<8} {'Price':<10} {'Value':<12}")
        print("-" * 50)
        
        for symbol, quantity in portfolio.items():
            price = StockPrices.get_price(symbol)
            value = price * quantity
            total_value += value
            
            stock_details.append({
                'symbol': symbol,
                'quantity': quantity, 
                'price': price,
                'value': value
            })
            
            print(f"{symbol:<8} {quantity:<8.0f} ${price:<9.2f} ${value:<11.2f}")
        
        print("-" * 50)
        print(f"{'TOTAL':<27} ${total_value:<11.2f}")
        
        return total_value, stock_details

# SECTION 4: FILE OUTPUT - Save to CSV (optional feature)
class PortfolioExporter:
    @staticmethod
    def save_to_csv(portfolio_data, filename="portfolio.csv"):
        """Save portfolio to CSV file"""
        try:
            with open(filename, 'w') as f:
                f.write("Symbol,Quantity,Price,Value\n")
                for stock in portfolio_data:
                    f.write(f"{stock['symbol']},{stock['quantity']},{stock['price']:.2f},{stock['value']:.2f}\n")
            print(f"\n Portfolio saved to {filename}")
            return True
        except Exception as e:
            print(f" Save failed: {e}")
            return False
    
    @staticmethod
    def save_to_txt(portfolio_data, total_value, filename="portfolio.txt"):
        """Save portfolio summary to text file"""
        try:
            with open(filename, 'w') as f:
                f.write("STOCK PORTFOLIO SUMMARY\n")
                f.write("=" * 30 + "\n\n")
                for stock in portfolio_data:
                    f.write(f"{stock['symbol']}: {stock['quantity']} shares @ ${stock['price']:.2f} = ${stock['value']:.2f}\n")
                f.write("\n" + "=" * 30 + "\n")
                f.write(f"TOTAL INVESTMENT: ${total_value:.2f}\n")
            print(f" Summary saved to {filename}")
            return True
        except Exception as e:
            print(f" Save failed: {e}")
            return False

# SECTION 5: PORTFOLIO DISPLAY - Formatted console output
class PortfolioDisplay:
    @staticmethod
    def show_final_summary(portfolio_data, total_value):
        """Display final portfolio statistics"""
        print("\n" + "=" * 60)
        print(" PORTFOLIO ANALYSIS COMPLETE")
        print("=" * 60)
        
        # Portfolio stats
        total_shares = sum(stock['quantity'] for stock in portfolio_data)
        avg_price_per_share = total_value / total_shares if total_shares > 0 else 0
        
        print(f" Total Investment Value:  ${total_value:>10.2f}")
        print(f" Total Shares:           {total_shares:>10.0f}")
        print(f" Average Price/Share:    ${avg_price_per_share:>10.2f}")
        print(f" Stocks Tracked:         {len(portfolio_data):>10}")
        
        # Top holdings
        if portfolio_data:
            top_holding = max(portfolio_data, key=lambda x: x['value'])
            print(f"\n Largest Holding: {top_holding['symbol']} (${top_holding['value']:.2f})")

# SECTION 6: MAIN APPLICATION - Complete workflow
def main():
    print(" STOCK PORTFOLIO TRACKER v1.0")
    print("Available stocks: AAPL, TSLA, GOOGL, MSFT, AMZN, NVDA\n")
    
    # Get user portfolio
    portfolio = PortfolioInput.get_stock_input()
    
    if not portfolio:
        print(" No stocks entered. Exiting.")
        return
    
    # Calculate investment value
    total_value, portfolio_data = InvestmentCalculator.calculate_portfolio_value(portfolio)
    
    # Display final summary
    PortfolioDisplay.show_final_summary(portfolio_data, total_value)
    
    # Optional file save
    save_choice = input("\n Save portfolio? (y/n): ").lower().strip()
    if save_choice == 'y':
        PortfolioExporter.save_to_csv(portfolio_data)
        PortfolioExporter.save_to_txt(portfolio_data, total_value)

if __name__ == "__main__":
    main()

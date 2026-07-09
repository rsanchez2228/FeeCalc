import streamlit as st
import pandas as pd

# Set up the web page title and header
st.set_page_config(page_title="FOCUS/OSP Fee Calculator", page_icon="🧮")
st.title("FOCUS/OSP Activity Fee Calculator")
st.subheader("4% Increase Processing Tool")
st.write("Enter your activity prices below and click 'Calculate Prices' to view the breakdown.")

# Create two options for data input
option = st.radio("Choose input method:", ("Type/Paste List", "Upload CSV File"))

if option == "Type/Paste List":
    # Text area for copy-pasting numbers
    input_data = st.text_area(
        "Paste prices here (one per line):", 
        value="10.00\n50.00\n100.00",
        help="Type or paste a column of numbers straight from Excel."
    )
    
    # Add a formal execution button
    calculate_clicked = st.button("Calculate Prices", type="primary")
    
    if calculate_clicked and input_data:
        try:
            # Convert text lines into a list of floats, skipping empty lines
            prices = [float(val.strip()) for val in input_data.split("\n") if val.strip()]
            
            # Create a DataFrame and calculate the 4% increase
            df = pd.DataFrame({"Original Price": prices})
            df["New Price (with 4% Fee)"] = (df["Original Price"] * 1.04).round(2)
            
            # Format the display copy for the web interface table
            df_display = df.copy()
            df_display["Original Price"] = df_display["Original Price"].map("${:,.2f}".format)
            df_display["New Price (with 4% Fee)"] = df_display["New Price (with 4% Fee)"].map("${:,.2f}".format)
            
            # Shift the index to start at 1 instead of 0
            df_display.index = df_display.index + 1
            
            # Display the formatted data table directly on the web page
            st.write("#### Item Breakdown")
            st.dataframe(df_display, use_container_width=True)
            
            # Allow user to download the original numerical result as a CSV
            csv = df.to_csv(index=False).encode('utf-8')
            st.download_button(
                label="Optional: Download Results (CSV)", 
                data=csv, 
                file_name="focus_osp_calculated_fees.csv", 
                mime="text/csv"
            )
            
        except ValueError:
            st.error("Please make sure all inputs are numbers only (do not include currency symbols like $).")

elif option == "Upload CSV File":
    uploaded_file = st.file_uploader("Upload a CSV file containing an 'Original Price' or 'Amount' column", type=["csv"])
    
    # Add a formal execution button
    calculate_clicked = st.button("Process CSV File", type="primary")
    
    if calculate_clicked and uploaded_file is not None:
        df = pd.read_csv(uploaded_file)
        
        # Look for a price column dynamically by checking column names
        price_col = [col for col in df.columns if 'price' in col.lower() or 'amount' in col.lower()]
        
        if price_col:
            col_name = price_col[0]
            df["New Price (with 4% Fee)"] = (df[col_name] * 1.04).round(2)
            
            # Format the display copy for the web interface
            df_display = df.copy()
            df_display[col_name] = df_display[col_name].map("${:,.2f}".format)
            df_display["New Price (with 4% Fee)"] = df_display["New Price (with 4% Fee)"].map("${:,.2f}".format)
            
            # Shift the index to start at 1 instead of 0
            df_display.index = df_display.index + 1
            
            st.write("#### Item Breakdown")
            st.dataframe(df_display, use_container_width=True)
            
            csv = df.to_csv(index=False).encode('utf-8')
            st.download_button(
                label="Optional: Download Updated CSV File", 
                data=csv, 
                file_name="focus_osp_updated_fees.csv", 
                mime="text/csv"
            )
        else:
            st.error("Could not find a column named 'Price' or 'Amount' in your uploaded file. Please check your column headers.")

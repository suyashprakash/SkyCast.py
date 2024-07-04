import streamlit as st
import requests

# Function to get weather data from openweathermap API
def get_weather_data(city, weather_api_key):
    base_url = 'https://api.openweathermap.org/data/2.5/weather?'
    complete_url = base_url + 'appid=' + weather_api_key + '&q=' + city
    response = requests.get(complete_url)
    return response.json()

# Function to generate weather description based on temperature
def generate_weather_description(data):
    try:
        # Convert temperature from Kelvin to Celsius
        temperature = data['main']['temp'] - 273.15
        description = data['weather'][0]['description']

        # Determine the custom description based on temperature range
        if 30 <= temperature < 40:
            custom_description = "Extreme heat outside"
        elif 40 <= temperature < 50:
            custom_description = "Excessive heat outside"
        elif 25 <= temperature < 30:
            custom_description = "Humidity outside"
        elif 20 <= temperature < 25:
            custom_description = "Cold outside"
        elif 15 <= temperature < 20:
            custom_description = "Very cold outside"
        elif 5 <= temperature < 15:
            custom_description = "Extreme cold outside"
        elif -30 <= temperature < 5:
            custom_description = "Excessive cold outside"
        else:
            custom_description = "Unusual temperature conditions outside"

        return f"The current weather in your city is {description} with a temperature of {temperature:.1f} degrees. {custom_description}."

    except Exception as e:
        return str(e)

# Main function to run the streamlit application
def main():
    # Sidebar configuration
    # Sidebar configuration
    st.sidebar.image("splogo.jp.jpeg", width=270)
    st.sidebar.title("Welcome To Suyash's Weather Forecast")
    city = st.sidebar.text_input("Enter the city name", "Patna")

    # API key
    weather_api_key = "f746e6effff9743917c8c3758fed2a52"

    # Button to fetch and display weather data
    submit = st.sidebar.button("Get Weather Data")

    if submit:
        st.title("☁️Weather updates for " + city + ":")
        with st.spinner("Fetching weather data...☔"):
            weather_data = get_weather_data(city, weather_api_key)
            print(weather_data)

            # Check if the city is found and display weather data
            if weather_data.get("cod") != 404:
                col1, col2 = st.columns(2)
                with col1:
                    st.metric("Temperature 🌡️", f"{weather_data['main']['temp'] - 273.15:.2f} °C")
                    st.metric("Humidity 💧", f"{weather_data['main']['humidity']}%")
                with col2:
                    st.metric("Pressure✈️", f"{weather_data['main']['pressure']} hPa")
                    st.metric("Wind Speed🍃", f"{weather_data['wind']['speed']} m/s")

                # Generate and display a friendly weather description
                weather_description = generate_weather_description(weather_data)
                st.write(weather_description)
            else:
                # Display an error message if the city is not found
                st.error("No city found or error occurred!")

if __name__ == "__main__":
    main()

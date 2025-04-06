# Safe-Route-Optimizer

Creating a comprehensive Safe-Route-Optimizer from scratch involves multiple components including real-time data gathering, processing, and path optimization algorithms. For simplicity, the example below is a basic structure that can be expanded with real-time data integration. The program will simulate interactive components such as crime data evaluation and traffic pattern analysis, and select the safest route based on this data.

Below is a simple structure of how such an application might look. It assumes you have access to some APIs or datasets for crime and traffic data.

```python
import requests
import logging
from collections import deque

# Configure logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

class SafeRouteOptimizer:
    def __init__(self, api_key):
        self.api_key = api_key
        self.crime_data_api = 'https://api.crime-data.example.com/data'
        self.traffic_data_api = 'https://api.traffic-data.example.com/data'
        self.geocoding_api = 'https://maps.googleapis.com/maps/api/geocode/json'
    
    def get_location_coordinates(self, location):
        """Get latitude and longitude of a location using Google Maps Geocoding API."""
        try:
            params = {
                'address': location,
                'key': self.api_key
            }
            response = requests.get(self.geocoding_api, params=params)
            response.raise_for_status()
            data = response.json()
            lat_lng = data['results'][0]['geometry']['location']
            return lat_lng['lat'], lat_lng['lng']
        except requests.exceptions.RequestException as e:
            logging.error(f"Error fetching location coordinates: {e}")
            raise

    def fetch_crime_data(self, lat, lng):
        """Fetch crime data for a specific latitude and longitude."""
        try:
            params = {
                'latitude': lat,
                'longitude': lng,
                'apikey': self.api_key
            }
            response = requests.get(self.crime_data_api, params=params)
            response.raise_for_status()
            return response.json()
        except requests.exceptions.RequestException as e:
            logging.error(f"Error fetching crime data: {e}")
            raise

    def fetch_traffic_data(self, lat, lng):
        """Fetch traffic data for a specific latitude and longitude."""
        try:
            params = {
                'latitude': lat,
                'longitude': lng,
                'apikey': self.api_key
            }
            response = requests.get(self.traffic_data_api, params=params)
            response.raise_for_status()
            return response.json()
        except requests.exceptions.RequestException as e:
            logging.error(f"Error fetching traffic data: {e}")
            raise

    def evaluate_safety(self, crime_data, traffic_data):
        """Simulate an evaluation function that combines crime and traffic data."""
        crime_score = sum(item['severity'] for item in crime_data) if crime_data else 0
        traffic_score = traffic_data.get('congestion_level', 0)
        safety_score = max(0, 100 - (crime_score + traffic_score))
        return safety_score

    def find_safest_route(self, start_location, end_location):
        try:
            start_lat, start_lng = self.get_location_coordinates(start_location)
            end_lat, end_lng = self.get_location_coordinates(end_location)
            
            # Dummy graph data using deque for a simple breadth-first search approach, replace with real routing logic
            route_graph = deque([
                (start_lat, start_lng),
                (end_lat, end_lng)
            ])
            
            safest_route = []
            highest_safety_score = -1

            while route_graph:
                current_node = route_graph.popleft()
                crime_data = self.fetch_crime_data(*current_node)
                traffic_data = self.fetch_traffic_data(*current_node)

                safety_score = self.evaluate_safety(crime_data, traffic_data)

                if safety_score > highest_safety_score:
                    highest_safety_score = safety_score
                    safest_route.append(current_node)
            
            return safest_route

        except Exception as e:
            logging.error(f"Error finding safest route: {e}")
            raise

if __name__ == '__main__':
    # Example API key, replace with a valid key
    api_key = 'your_google_maps_api_key'

    optimizer = SafeRouteOptimizer(api_key)

    start_location = "1600 Amphitheatre Parkway, Mountain View, CA"
    end_location = "1 Infinite Loop, Cupertino, CA"

    try:
        route = optimizer.find_safest_route(start_location, end_location)
        print(f'Safest route from {start_location} to {end_location}: {route}')
    except Exception as e:
        logging.error(f'An error occurred while calculating the safest route: {e}')
```

### Explanation and Assumptions:

1. **APIs Used**:
    - Fake URLs for the crime data and traffic data APIs (`api.crime-data.example.com` and `api.traffic-data.example.com`). Replace these with actual providers or datasets.
    - Google Maps Geocoding API is used for converting addresses into geographic coordinates.

2. **Error Handling**:
    - Requests to external services include basic error handling using `try`...`except`.
    - Logging statements capture errors and operational information.

3. **Safety Evaluation**:
    - A mock evaluation function that combines crime severity with traffic congestion levels to produce a safety score.

4. **Routing Logic**:
    - A very basic approach using a list to simulate routing, replace with actual routing logic for real applications.

5. **Further Enhancements**:
    - Complete integration with actual APIs.
    - Develop a more comprehensive routing algorithm (e.g., A* search, Dijkstra's algorithm).
    - Use more sophisticated safety scoring mechanisms that better reflect real-world scenarios.
    - Real-time updates and dynamic rerouting capabilities. 

Replace the API URLs, keys, and implement a genuine routing algorithm suited to your application context for a production-level implementation.
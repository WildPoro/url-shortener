import azure.functions as func
import random
import string
import logging

# Temporary in-memory dictionary to store short-url to original-url mappings
url_map = {}

# Function to generate a random short ID
def generate_short_id(length=6):
    return ''.join(random.choices(string.ascii_letters + string.digits, k=length))

# Main function to handle HTTP requests
def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info("Function triggered!")

    method = req.method  # Check if it's GET or POST

    if method == "POST":
        try:
            # Parse incoming JSON body
            data = req.get_json()
            original_url = data.get("url")

            # Generate a unique short ID and store the mapping
            id = generate_short_id()
            url_map[id] = original_url  # This stores the short ID → original URL

            logging.info(f"URL map updated with: /{id}")

            # Return the short URL to the user
            return func.HttpResponse(f"your short url is: /{id}", status_code=200)

        except Exception as e:
            # If something goes wrong (bad JSON, etc.), return error
            logging.error(f"Error in POST request: {e}")
            return func.HttpResponse("something went wrong", status_code=400)

    elif method == "GET":
        # Get the short_id from the route
        id = req.route_params.get("id")
        logging.info(f"Looking for short ID: {id}")

        original_url = url_map.get(id)  # Look it up in the dictionary
        logging.info(f"Original URL found: {original_url}")

        if original_url:
            # Redirect to original URL if found
            return func.HttpResponse(status_code=302, headers={"Location": original_url})
        else:
            # If not found, return 404
            return func.HttpResponse("url not found", status_code=404)

    # If not GET or POST, return unsupported method
    logging.warning("Unsupported method")
    return func.HttpResponse("Unsupported method", status_code=400)

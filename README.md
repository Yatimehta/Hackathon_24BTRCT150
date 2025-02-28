import requests
from bs4 import BeautifulSoup
from urllib.parse import urlparse

def get_news_source(url):
    try:
        response = requests.get(url, timeout=5)
        response.raise_for_status()
        soup = BeautifulSoup(response.text, "html.parser")
        
        meta_tags = soup.find_all("meta")
        for tag in meta_tags:
            if tag.get("property") == "og:site_name":
                return tag.get("content", "Unknown Source")
        
        parsed_url = urlparse(url)
        return parsed_url.netloc if parsed_url.netloc else "Unknown Source"
    
    except requests.exceptions.RequestException as e:
        return f"Error fetching source: {e}"

def check_trusted_source(source, trusted_sources):
    return source.lower() in (s.lower() for s in trusted_sources)

trusted_sources = ["BBC News", "CNN", "The New York Times", "The Guardian", "Reuters", "Al Jazeera"]

def main():
    print("Fake News Detector 🔍")
    url = input("Enter the news article URL: ").strip()
    
    if not url.startswith("http"):
        print("Invalid URL. Please enter a full URL (including https:// or http://).")
        return
    
    source = get_news_source(url)
    
    if "Error fetching source" in source:
        print(source)
    elif check_trusted_source(source, trusted_sources):
        print(f"✅ This article is from a trusted source: {source}")
    else:
        print(f"⚠️ Warning: The source '{source}' is not in the trusted list. Verify information from multiple sources.")

if __name__ == "__main__":
    main()

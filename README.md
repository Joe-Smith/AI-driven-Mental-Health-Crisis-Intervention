# AI-driven-Mental-Health-Crisis-Intervention
開発AIを利用した精神保健危機介入ツールは、ソーシャルメディア上の兆候を検出し、リスクのある個人にリソースを提供します。
from flask import Flask, request, jsonify
import nltk
from nltk.sentiment import SentimentIntensityAnalyzer

# Download VADER lexicon for sentiment analysis
nltk.download('vader_lexicon')

app = Flask(__name__)

def analyze_sentiment(text):
    sia = SentimentIntensityAnalyzer()
    score = sia.polarity_scores(text)
    return score

def detect_crisis(score):
    # Example criteria: negative sentiment score below -0.5 indicates crisis
    if score['compound'] < -0.5:
        return True
    return False

def provide_resources():
    # Example resources
    return {
        "helpline": "1-800-123-HELP",
        "website": "https://mentalhealth.example.org",
        "chat": "https://chat.mentalhealth.example.org"
    }

@app.route('/analyze', methods=['POST'])
def analyze_post():
    data = request.json
    text = data.get('text')
    
    if text:
        sentiment_score = analyze_sentiment(text)
        if detect_crisis(sentiment_score):
            resources = provide_resources()
            return jsonify({"crisis_detected": True, "resources": resources}), 200
        else:
            return jsonify({"crisis_detected": False, "message": "No signs of crisis detected."}), 200
    else:
        return jsonify({"error": "No text provided."}), 400

if __name__ == '__main__':
    app.run(debug=True, port=5000)

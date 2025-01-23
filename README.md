import random

# This AI will try to guess if a number is greater than 50
class SimpleAI:
    def __init__(self):
        self.bias = 0
        self.learning_rate = 0.1

    def predict(self, number):
        # If the number plus our bias is greater than 50, predict 'greater'
        return number + self.bias > 50

    def learn(self, number, actual):
        # Adjust the bias based on whether we guessed correctly
        if self.predict(number) != actual:
            if actual:  # If the number is actually greater than 50
                self.bias += self.learning_rate
            else:
                self.bias -= self.learning_rate

# Training the AI
ai = SimpleAI()
for _ in range(1000):  # Train over 1000 iterations
    number = random.randint(0, 100)
    actual = number > 50
    ai.learn(number, actual)

# Testing the AI
correct = 0
total = 100
for _ in range(total):
    number = random.randint(0, 100)
    prediction = ai.predict(number)
    if prediction == (number > 50):
        correct += 1

print(f"AI Accuracy: {correct/total:.2f}")

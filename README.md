# role-based-chat-with-ollama
import ollama

# Use faster model
MODEL = "tinyllama"

# Define roles
roles = {
    "1": {
        "name": "Python Tutor",
        "prompt": "You are a patient Python tutor. Explain concepts clearly with simple examples."
    },
    "2": {
        "name": "Fitness Coach",
        "prompt": "You are a professional fitness coach. Give practical health and workout advice."
    },
    "3": {
        "name": "Travel Guide",
        "prompt": "You are a travel expert. Suggest destinations and travel tips."
    }
}

# Function to choose role
def choose_role():
    print("\nAvailable Roles:")
    for key, role in roles.items():
        print(f"{key}. {role['name']}")

    choice = input("Pick a role: ")
    return roles.get(choice)

# Main chat function
def chat():
    role = choose_role()

    if not role:
        print("Invalid choice")
        return

    print(f"\nRole set: {role['name']}")
    print("Type your message (or 'switch' to change role, 'quit' to exit)\n")

    # Conversation memory
    messages = [
        {"role": "system", "content": role["prompt"]}
    ]

    while True:
        user_input = input("You: ")

        # Exit
        if user_input.lower() == "quit":
            print("Goodbye!")
            break

        # Switch role
        if user_input.lower() == "switch":
            role = choose_role()
            if not role:
                print("Invalid choice")
                continue

            print(f"\nRole set: {role['name']}")
            messages = [
                {"role": "system", "content": role["prompt"]}
            ]
            continue

        # Add user message
        messages.append({"role": "user", "content": user_input})

        try:
            # Get response from Ollama
            response = ollama.chat(
                model=MODEL,
                messages=messages
            )

            reply = response["message"]["content"]

            # Print response
            print(f"\n{role['name']}: {reply}\n")

            # Save response to memory
            messages.append({"role": "assistant", "content": reply})

        except Exception as e:
            print("Error:", e)
            print("Make sure Ollama is running and model is installed.")

# Run program
if __name__ == "__main__":
    chat()

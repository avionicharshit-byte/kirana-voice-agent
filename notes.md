# What it does
- A voice assistant for a kirana shop, built on livekit agents. ita has currently 3 tools 
  - add item to bill
  - remove item from bill
  - read items from bill

# Bugs i found
1. the agent made an extra tool call with "unknown" as the item.
2. it said it removed 1kg, but the bill never changed, i saw in the log when read bill function called it was showing earlier items but llm replied confidently that it removed the items from the bill
3. tool call didn't always come in the order i said things

# How i fixed it
1. added a guardrail check inside the add item function tool , so empty or unknowm items get refused
2. switched the bill from a list to a dictionary , so i can remove specific quantities from items like 2 out of 5kg of rice
3. added instructions like always use read bill , never read from memory, and never pretend a tool worked

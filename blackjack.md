## blackjack

This one was a lot of fun. I was just playing for a while and happened to be lucky, so I was trying to spot a pattern. After combing through the code for a bit I was struggling to find a place where it was vulnerable until I saw the function that prompts the user to enter a bet:

```
int betting() //Asks user amount to bet
{
 printf("\n\nEnter Bet: $");
 scanf("%d", &bet);
 
 if (bet > cash) //If player tries to bet more money than player has
 {
        printf("\nYou cannot bet more money than you have.");
        printf("\nEnter Bet: ");
        scanf("%d", &bet);
        return bet;
 }
 else return bet;
}
```

If we enter more money than we have the first time, we will get re-prompted. But if we enter the same amount a the re-prompt, it lets us pretend like we have the money. The challenge hinted that we needed a million dollars, so all we need to do is try to bit a $1,000,000 twice (even if it says we don't have enough) and win a game in order to win (technically just need to bet $500,000 but i was being crazy).

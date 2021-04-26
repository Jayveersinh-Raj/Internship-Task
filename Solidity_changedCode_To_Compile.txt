// SPDX-License-Identifier: GPL-3.0
    
pragma solidity ^0.4.24;


contract rps
{
    mapping (string => mapping(string => int)) payoffMatrix;
    address public player1;
    address public player2;
    string public player1Choice;
    string public player2Choice;

    modifier notRegisteredYet()
    {
        require (msg.sender == player1 || msg.sender == player2);
        _;
    }
    
    modifier sentEnoughCash(uint amount)
    {
        require (msg.value < amount);
            _;
    }
    
    constructor() public
    {   // constructor
        payoffMatrix["rock"]["rock"] = 0;
        payoffMatrix["rock"]["paper"] = 2;
        payoffMatrix["rock"]["scissors"] = 1;
        payoffMatrix["paper"]["rock"] = 1;
        payoffMatrix["paper"]["paper"] = 0;
        payoffMatrix["paper"]["scissors"] = 2;
        payoffMatrix["scissors"]["rock"] = 2;
        payoffMatrix["scissors"]["paper"] = 1;
        payoffMatrix["scissors"]["scissors"] = 0;
    }
    
    function getWinner()  returns (int x)
    {
        return payoffMatrix[player1Choice][player2Choice];
    }
    
    function play(string choice) returns (int w)
    {
        if (msg.sender == player1)
            player1Choice = choice;
        else if (msg.sender == player2)
            player2Choice = choice;
        if (bytes(player1Choice).length != 0 && bytes(player2Choice).length != 0)
        {
            int winner = payoffMatrix[player1Choice][player2Choice];
            if (winner == 1)
                player1.send(this.balance);
            else if (winner == 2)
                player2.send(this.balance);
            else
            {
                player1.send(this.balance/2);
                player2.send(this.balance);
            }
             
            // unregister players and choices
            player1Choice = "";
            player2Choice = "";
            player1 = 0;
            player2 = 0;
            return winner;
        }
        else 
            return -1;
    }
    
// HELPER FUNCTIONS (not required for game)

    function getMyBalance () returns (uint amount)
    {
        return msg.sender.balance;
    }
    
    function getContractBalance () returns (uint amount)
    {
        return this.balance;
    }
    
    function register()
        sentEnoughCash(5)
        notRegisteredYet()
    {
        if (player1 == 0)
            player1 = msg.sender;
        else if (player2 == 0)
            player2 = msg.sender;
    }
    
    function AmIPlayer1() returns (bool x)
    {
        return msg.sender == player1;
    }
    
    function AmIPlayer2()  returns (bool x)
    {
        return msg.sender == player2;
    }

    
    function checkBothNotNull() returns (bool x)
    {
        return (bytes(player1Choice).length == 0 && bytes(player2Choice).length == 0);
    }

// \HELPER FUNCTIONS

}
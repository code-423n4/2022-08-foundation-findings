1.

It is unnecesary to iniliaze variables in for loops

Contract: MarketFees.sol

	line 126
	line 198
	line 484

Recommendation:

	for (uint256 i; i < creatorRecipients.length; ++i)
	for (uint256 i; i < creatorShares.length; ++i)
	for (uint256 i; i < creatorRecipients.length; ++i)
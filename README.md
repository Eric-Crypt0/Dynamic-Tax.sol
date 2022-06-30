# Dynamic-Tax.sol
Blockchain Solidity - A Dynamic Tax based on Price impact


  uint256 private _baseFee = 10;

    uint256 private _moderateBuyImpact = 1;
    uint256 private _highBuyImpact = 3;
    uint256 private _extremeBuyImpact = 5;

    uint256 private _moderateBuyFee = 20;   // 2% fee discount
    uint256 private _highBuyFee = 40;       // 4% fee discount
    uint256 private _extremeBuyFee = 60;    // 6% fee discount

    uint256 private _moderateSellImpact = 1;
    uint256 private _highSellImpact = 3;
    uint256 private _extremeSellImpact = 5;

    uint256 private _moderateSellFee = 50;  // 5% fee increase
    uint256 private _highSellFee = 120;     // 12% fee increase
    uint256 private _extremeSellFee = 200;  // 20% fee increase


if(from == uniswapV2Pair && to != address(uniswapV2Router) && !_isExcludedFromFee[to]) {
                require(_tradingOpen, "Trading not yet enabled.");                
                
                if(_cooldownEnabled && !_isTimelockExempt[to]){
                    require(_cooldownTimer[to] < block.timestamp, "Please wait for cooldown between buys");
                    _cooldownTimer[to] = block.timestamp + _cooldownTimerInterval;
                }

                if (amount >= balanceOf(uniswapV2Pair).mul(_extremeBuyImpact).div(100)) {
                    _teamFee = _baseFee - _baseFee.mul(_extremeBuyFee).div(100);
                } else if (amount >= balanceOf(uniswapV2Pair).mul(_highBuyImpact).div(100)) {
                    _teamFee = _baseFee - _baseFee.mul(_highBuyFee).div(100);
                } else if (amount >= balanceOf(uniswapV2Pair).mul(_moderateBuyImpact).div(100)) {
                    _teamFee = _baseFee - _baseFee.mul(_moderateBuyFee).div(100);
                } else {
                    _teamFee = _baseFee;
                }
                
                uint walletBalance = balanceOf(address(to));
                require(amount.add(walletBalance) <= _tTotal.mul(_maxWalletPercentage).div(100), "amount exceeds max wallet holdings");
                if (_maxBuyAmount > 0) {
                    require(amount <= _maxBuyAmount, "amount exceeds max buy");
                }

                if(_getFeeOnBuy && contractTokenBalance > minContractTokensToSwap) {
                    if(!_swapAll) {
                        contractTokenBalance = minContractTokensToSwap;
                    }

                    if (_liquidityFeePercentage > 0) {
                        swapAndLiquify(contractTokenBalance);
                    } else {
                        swapWithoutLiquify(contractTokenBalance);
                    }     
                }
            }

            if(!_inSwap && from != uniswapV2Pair && _tradingOpen) {
                
                if(_cooldownEnabled && !_isTimelockExempt[from]){
                    require(_cooldownTimer[from] < block.timestamp, "Please wait for cooldown between sells");
                    _cooldownTimer[from] = block.timestamp + _cooldownTimerInterval;
                }

                if (amount >= balanceOf(uniswapV2Pair).mul(_extremeSellImpact).div(100)) {
                    _teamFee = _baseFee + _baseFee.mul(_extremeSellFee).div(100);
                } else if (amount >= balanceOf(uniswapV2Pair).mul(_highSellImpact).div(100)) {
                    _teamFee = _baseFee + _baseFee.mul(_highSellFee).div(100);
                } else if (amount >= balanceOf(uniswapV2Pair).mul(_moderateSellImpact).div(100)) {
                    _teamFee = _baseFee + _baseFee.mul(_moderateSellFee).div(100);
                } else {
                    _teamFee = _baseFee;
                }

                if (_maxSellAmount > 0) {
                    require(amount <= _maxSellAmount, "amount exceeds max sell");
                }

                function setBaseFee(uint256 fee) external onlyOwner() {
        require(fee <= 10, "Base fee must be less than 10");
        _baseFee = fee;
    }
        
    function setTaxFee(uint256 tax) external onlyOwner() {
        require(tax <= 5, "tax must be less than 5");
        _taxFee = tax;
    }

    function updateDynamicFees(uint256 moderateBuyImpact, uint256 highBuyImpact, uint256 extremeBuyImpact) public onlyOwner {
        _moderateBuyImpact = moderateBuyImpact;
        _highBuyImpact = highBuyImpact;
        _extremeBuyImpact = extremeBuyImpact;
    }

    function updateBuyFees(uint256 moderateBuyFee, uint256 highBuyFee, uint256 extremeBuyFee) public onlyOwner {
        _moderateBuyFee = moderateBuyFee;
        _highBuyFee = highBuyFee;
        _extremeBuyFee = extremeBuyFee;
    }

    function updateDynamicDump(uint256 moderateSellImpact, uint256 highSellImpact, uint256 extremeSellImpact) public onlyOwner {
        _moderateSellImpact = moderateSellImpact;
        _highSellImpact = highSellImpact;
        _extremeSellImpact = extremeSellImpact;
    }

    function updateSellFees(uint256 moderateSellFee, uint256 highSellFee, uint256 extremeSellFee) public onlyOwner {
        _moderateSellFee = moderateSellFee;
        _highSellFee = highSellFee;
        _extremeSellFee = extremeSellFee;
    }

    function setNoTaxMode(bool onoff) external onlyOwner() {
        _noTaxMode = onoff;
    }

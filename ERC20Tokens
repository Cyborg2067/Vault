// SPDX-License-Identifier: MIT

pragma solidity ^0.8.25;

library SafeMath {
    function tryAdd(uint256 a, uint256 b) internal pure returns (bool success, uint256 c) {
        unchecked {
            c = a + b;
            if (c < a) c = 0;
            else success = true;
        }
    }

    function trySub(uint256 a, uint256 b) internal pure returns (bool success, uint256 c) {
        unchecked {
            if (b <= a) {
                c = a - b;
                success = true;
            }
        }
    }

    function tryMul(uint256 a, uint256 b) internal pure returns (bool success, uint256 c) {
        unchecked {
            if (a == 0) success = true;
            else {
                c = a * b;
                if (c / a != b) {
                    c = 0;
                } else {
                    success = true;
                }
            }
        }
    }

    function tryDiv(uint256 a, uint256 b) internal pure returns (bool success, uint256 c) {
        unchecked {
            if (b > 0) {
                c = a / b;
                success = true;
            }
        }
    }

    function tryMod(uint256 a, uint256 b) internal pure returns (bool success, uint256 c) {
        unchecked {
            if (b > 0) {
                c = a % b;
                success = true;
            }
        }
    }

    function add(uint256 a, uint256 b) internal pure returns (uint256 c) {
        unchecked {
            c = a + b;
            require(c >= a, "SafeMath: addition overflow");
        }
    }

    function sub(uint256 a, uint256 b) internal pure returns (uint256 c) {
        unchecked {
            require(b <= a, "SafeMath: subtraction overflow");
            c = a - b;
        }
    }

    function mul(uint256 a, uint256 b) internal pure returns (uint256 c) {
        unchecked {
            c = a * b;
            require(c / a == b, "SafeMath: multiplication overflow");
        }
    }

    function div(uint256 a, uint256 b) internal pure returns (uint256 c) {
        unchecked {
            require(b > 0, "SafeMath: division by zero");
            c = a / b;
        }
    }

    function mod(uint256 a, uint256 b) internal pure returns (uint256 c) {
        unchecked {
            require(b > 0, "SafeMath: modulo by zero");
            return a % b;
        }
    }

    function sub(uint256 a, uint256 b, string memory errorMessage) internal pure returns (uint256 c) {
        unchecked {
            require(b <= a, errorMessage);
            c = a - b;
        }
    }

    function div(uint256 a, uint256 b, string memory errorMessage) internal pure returns (uint256 c) {
        unchecked {
            require(b > 0, errorMessage);
            c = a / b;
        }
    }

    function mod(uint256 a, uint256 b, string memory errorMessage) internal pure returns (uint256 c) {
        unchecked {
            require(b > 0, errorMessage);
            c = a % b;
        }
    }
}

abstract contract Context {
    function _msgSender() internal view virtual returns (address) {
        return msg.sender;
    }

    function _msgData() internal view virtual returns (bytes calldata) {
        return msg.data;
    }
}

abstract contract Ownable is Context {
    address private _owner;

    error OwnableUnauthorizedAccount(address account);

    error OwnableInvalidOwner(address owner);

    event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);

    constructor(address initialOwner) {
        if (initialOwner == address(0)) {
            revert OwnableInvalidOwner(address(0));
        }
        _transferOwnership(initialOwner);
    }

    modifier onlyOwner() {
        _checkOwner();
        _;
    }

    function owner() public view virtual returns (address) {
        return _owner;
    }

    function _checkOwner() internal view virtual {
        if (owner() != _msgSender()) {
            revert OwnableUnauthorizedAccount(_msgSender());
        }
    }

    function renounceOwnership() public virtual onlyOwner {
        _transferOwnership(address(0));
    }

    function transferOwnership(address newOwner) public virtual onlyOwner {
        if (newOwner == address(0)) {
        revert OwnableInvalidOwner(address(0));
        }
        _transferOwnership(newOwner);
    }

    function _transferOwnership(address newOwner) internal virtual {
        address oldOwner = _owner;
        _owner = newOwner;
        emit OwnershipTransferred(oldOwner, newOwner);
    }
}

abstract contract ReentrancyGuard {
    uint256 private constant NOT_ENTERED = 1;
    uint256 private constant ENTERED = 2;
    uint256 private _status;

    error ReentrancyGuardReentrantCall();

    constructor() {
        _status = NOT_ENTERED;
    }

    modifier nonReentrant() {
        _nonReentrantBefore();
        _;
        _nonReentrantAfter();
    }

    function _nonReentrantBefore() private {
        if (_status == ENTERED) {
            revert ReentrancyGuardReentrantCall();
        }
        _status = ENTERED;
    }

    function _nonReentrantAfter() private {
        _status = NOT_ENTERED;
    }

    function _reentrancyGuardEntered() internal view returns (bool) {
        return _status == ENTERED;
    }
}

interface IERC20 {
    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);

    function totalSupply() external view returns (uint256);
    function balanceOf(address account) external view returns (uint256);
    function transfer(address to, uint256 value) external returns (bool);
    function allowance(address owner, address spender) external view returns (uint256);
    function approve(address spender, uint256 value) external returns (bool);
    function transferFrom(address from, address to, uint256 value) external returns (bool);
}

interface IERC20Metadata is IERC20 {
    function name() external view returns (string memory);
    function symbol() external view returns (string memory);
    function decimals() external view returns (uint8);
}

contract Vault1 is Context, Ownable, ReentrancyGuard {
    using SafeMath for uint256;

    mapping(address => uint256) private _balances;
    mapping(address => uint256) private _deposits;
   
    IERC20 public token;

    constructor(address Owner, address Contract) Ownable(Owner) {
        token = IERC20(Contract); 
    }

    modifier _deposit(address Owner) {
        require(Owner != address(0), "ERC20: transfer to the zero address");
        require(Owner != address(this), "ERC20: transfer to the contract address");
        _;
    }

    function deposit(uint256 _amount) public nonReentrant _deposit(msg.sender) {
        require(token.transferFrom(msg.sender, address(this), _amount), "deposit failed");
        _deposits[msg.sender] = _deposits[msg.sender].add(_amount);
    }

    modifier _withdraw(address onlyOwner) {
        require(onlyOwner != address(0), "ERC20: transfer to the zero address");
        require(onlyOwner != address(this), "ERC20: transfer to the contract address");
        _;
    }

    function withdraw(uint256 _amount) public nonReentrant _withdraw(msg.sender) {
        uint256 senderBalance = _deposits[msg.sender];
        require(senderBalance >= _amount, "withdraw amount exceeds deposit");
        require(token.transfer(msg.sender, _amount), "withdraw failed");
        
        _deposits[msg.sender] = senderBalance.sub(_amount);
    }

    function beforeTokenTransfer(address sender, address recipient, uint256 amount) internal {
        uint256 senderBalance = _balances[sender];
        require(senderBalance >= amount, "ERC20: transfer amount exceeds balance");
        _balances[sender] = senderBalance - amount;
        _balances[recipient] += amount;
    }
    
    function balanceOf(address account) public view returns (uint256) {
        return _deposits[account];
    }
    
    function _msgSender() internal view override returns (address) {
        return msg.sender;
    }
}

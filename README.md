change brand name


수정 위치
utils.js 안의 function identifyMiner(coinbaseTx, blockHeight)를 찾으면,
맨 첫 부분에 이런 코드가 있습니다:

function identifyMiner(coinbaseTx, blockHeight) {
    if (coinbaseTx == null || coinbaseTx.vin == null || coinbaseTx.vin.length == 0) {
        return null;
    }
이 바로 아래에 다음 코드를 넣으시면 됩니다.





    // --- Bitsteal 전용: 화이트리스트 주소만 라벨 ---
    const ALLOWLIST = {
        "bc1q6wvmr7nx4vdx0kuheq7da36scc9cuekv6zrkl7": { name: "Bitsteal Miner" }
        // 필요하면 다른 주소도 추가
    };

    let payoutAddress = null;
    if (coinbaseTx.vout && coinbaseTx.vout.length > 0) {
        for (let i = 0; i < coinbaseTx.vout.length; i++) {
            const vout = coinbaseTx.vout[i];
            if (vout.value > 0) {
                const addr = getVoutAddress(vout);
                if (addr) {
                    payoutAddress = addr;
                    break;
                }
            }
        }
    }

    if (payoutAddress && ALLOWLIST[payoutAddress]) {
        const minerInfo = Object.assign({}, ALLOWLIST[payoutAddress]);
        minerInfo.identifiedBy = "allowlisted payout address " + payoutAddress;
        return minerInfo;
    }

    if (payoutAddress) {
        return {
            name: "Unknown",
            type: "unknown",
            identifiedBy: "unregistered payout address " + payoutAddress
        };
    }
    // --- Bitsteal 전용 끝 ---


# HackBS
Buy/Sell position analysis tool (chart and lob)

## Reference
Chart : trading-vue-js (https://github.com/tvjsx/trading-vue-js)

LOB(Limit Order Book) : kiwoom

## Live Demo 

PC only

https://mimmic.github.io/HackBS/

## Data Format


data_format = {

  '종목코드' :  {
  
    'data' : {
        'ohlcv' : [[gmtime, open, low, close, volume] ...] => 1분봉

        'onchart' : [{
            'name' : 'BS_Marker',
            'type' : 'BS_Marker',
            'settings' : {'z-index': 5},
            'data' : [[gmtime, price, 'BSBSSS', [매매내역 volume들], '07/21 09:00'] ...] => 1분간의 매매내역
        }]
    }
    
    'hoga' : [['09:00:00', total_offerrem, total_bidrem, max(offerrem, bidrem), 
    
    offerrem1, ..., offerrem10, offerho1, ..., offerho10, bidrem1, ..., bidrem10, ..., bidho1, ..., bidho10 ] ... ] => 없어도됨
  
  } ...
}

매매내역 : 1분 동안 Buy, Sell들을 누적해서 보여줌, Buy -> B, Sell -> S, Buy Sell Buy Buy 이면 BSBB

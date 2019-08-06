# B-Handler
Simple handler for a B: (Browser) CIO Atari device

The following code in Atari Basic demonstrates the handler usage:

```
OPEN #3,8,0,"B:"
PRINT #3;"http://atari.pl/hsc/?x=003000129"
CLOSE #3
```

and an example in Assembler (JSR write2Bdevice):

```
write2Bdevice
    JSR lookup
    BPL do_cio
    RTS

do_cio
    LDA #$03 ; open
    STA ICCMD,X
    LDA #<browser_device
    STA ICBAL,X
    LDA #>browser_device
    STA ICBAH,X
    LDA #$00
    STA ICBLH,X
    LDA #$02
    STA ICBLL,X
    LDA #$08
    STA ICAX1,X
    LDA #$00
    STA ICAX2,X
    JSR CIOV
    
    LDA #$09 ; write
    STA ICCMD,X
    LDA #<url
    STA ICBAL,X
    LDA #>url
    STA ICBAH,X
    LDA #$00
    STA ICBLH,X
    LDA #url_len
    STA ICBLL,X
    JSR CIOV
    
    LDA #$0C ; close
    STA ICCMD,X
    JMP CIOV

LOOKUP  LDX #$00 ; search for a free CIO channel
        LDY #$01
LOOP    LDA ICCHID,X
        CMP #$FF
        BEQ FOUND
        TXA
        CLC
        ADC #$10
        TAX
        BPL LOOP
        LDY #-95 ; error code "TOO MANY CHANNELS OPEN"
FOUND   RTS      ; X contains the offset for a channel
```

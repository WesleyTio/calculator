<template>
    <div class="calculator">
        <Display :value="displayValue"/>
        <Button label="AC" triple @onClick="clearMemory"/>
        <Button label="/" operation @onClick="setOperation"/>
        <Button label="7" @onClick="addDigit"/>
        <Button label="8"  @onClick="addDigit"/>
        <Button label="9"  @onClick="addDigit"/>
        <Button label="*" operation  @onClick="setOperation"/>
        <Button label="4"  @onClick="addDigit"/>
        <Button label="5"  @onClick="addDigit"/>
        <Button label="6"  @onClick="addDigit"/>
        <Button label="-" operation  @onClick="setOperation"/>
        <Button label="1"  @onClick="addDigit"/>
        <Button label="2"  @onClick="addDigit"/>
        <Button label="3"  @onClick="addDigit"/>
        <Button label="+"  operation  @onClick="setOperation"/>
        <Button label="0" double  @onClick="addDigit"/>
        <Button label="."  @onClick="addDigit"/>
        <Button label="="  operation  @onClick="setOperation"/>

    </div>
</template>

<script>
import Display from '../components/Display'
import Button from '../components/Button'
export default {
    data: function(){
        return {
            displayValue: "0",
            overflow: false,
            clearDisplay: false,
            operation: null,
            values:[0,0],
            current: 0

        }
    },
    components: { Button, Display},
    methods: {
        clearMemory(){
            Object.assign(this.$data, this.$options.data())
        },
        setOperation(operation) {

           /*  if(this.displayValue.indexOf("Overflow") > -1){
                //this.clearMemory()
                Object.assign(this.$data, this.$options.data())
            } */
            if (this.current === 0) {
                this.operation = operation
                this.current = 1
                this.clearDisplay = true
                this.overflow = false
            }else {
                const equals = operation === "="
                const currentOperation = this.operation
                try {
                    const value = eval(`${this.values[0]} ${currentOperation} ${this.values[1]}`)
                    if( value <= 999999999999){
                            this.values[0] = parseFloat(value.toFixed(10))
                            const displayLength = this.values[0] + ""
                            if( displayLength.length > 12){
                                this.displayValue = "Overflow"
                                this.overflow = false
                                this.values[0] = 0
                                this.values[1] = 0
                                this.operation = null
                                this.current = 0
                                this.clearDisplay = false
                                return
                            }

                    }else{
                        this.displayValue = "Overflow"
                        this.overflow = false
                        this.values[0] = 0
                        this.values[1] = 0
                        this.operation = null
                        this.current = 0
                        this.clearDisplay = false
                        return
                    }
                    
                } catch (error) {
                    this.$emit('onError', error) 
                }

                this.values[1] = 0
                this.displayValue = this.values[0]
                this.operation = equals ? null : operation
                this.current = equals ? 0 : 1
                this.clearDisplay = !equals
            }
        },
        addDigit(n){
            /* if(this.displayValue.indexOf("Overflow") > -1){
                //this.clearMemory()
                Object.assign(this.$data, this.$options.data())

            } */
            if(this.overflow === true && this.clearDisplay === false){
                console.log("overflow")
                return
            }
            if ((n === "." && this.displayValue.includes("."))){
                return
            }
            const clearDisplay = this.displayValue === "0" || this.clearDisplay
            const currentValue = clearDisplay ? "" : this.displayValue
            const displayValue = currentValue + n
            this.displayValue = displayValue
            if(this.displayValue.length>=12){
                this.overflow = true
                console.log("Overflow Active")
            }
            console.log(this.displayValue.length)
            

            this.clearDisplay = false
            console.log(this.current + " indx")
            this.values[this.current] = this.displayValue
        }

    }
}
</script>

<style>
.calculator {
    height: 320px;
    width: 235px;
    border-radius: 5px;
    overflow: hidden;

    display: grid;
    grid-template-columns: repeat(4,25%);
    grid-template-rows: 1fr 48px 48px 48px 48px ;

}

</style>
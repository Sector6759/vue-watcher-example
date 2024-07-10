<script setup lang="ts">
import { ref, watch } from "vue";

const myRef = ref({
  primitiveProperty: "initial",
  nonPrimitiveProperty: new String("initial"),
});

function touchMyRef() {
  myRef.value.primitiveProperty = "another"; // Setting primitiveProperty to another value
  myRef.value.primitiveProperty = "initial"; // Setting primitiveProperty to the initial value before next tick happens
  myRef.value.nonPrimitiveProperty = new String("another"); //  Setting nonPrimitiveProperty to another value
  myRef.value.nonPrimitiveProperty = new String("initial"); //  Setting nonPrimitiveProperty to the initial value before next tick happens
}

function callback(newValue: any, oldValue: any) {
  console.log(newValue, oldValue);
}

watch(() => myRef.value.primitiveProperty, callback); // Expected 😊: callback not triggered when calling `touchMyRef()`
watch(() => myRef.value.nonPrimitiveProperty, callback); // NOT EXPECTED 🤨: callback TRIGGERED when calling `touchMyRef()`
watch(() => myRef.value, callback); // Because of the unexpected second callback being triggered, I am not sure what to expect here, but the callback is not triggered when calling `touchMyRef()`,

setInterval(touchMyRef, 1000);
</script>

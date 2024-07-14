<script setup lang="ts">
import { ref, watch } from "vue";

const objectRef = ref({ foo: "bar" });
const stringRef = ref("hello");

function touchRefs() {
  objectRef.value.foo = "baz"; // Apply changes
  objectRef.value.foo = "bar"; // Revert before next tick happens
  stringRef.value = "world"; // Apply changes
  stringRef.value = "hello"; // Revert before next tick happens
}

function callback(newValue: any, oldValue: any) {
  console.log(newValue, oldValue);
}

// The first watcher's getter function returns a primitive, the others return a non-primitive, which somehow decides if the watcher gets triggered or not.
// Even though `touchRefs()` alters the refs, the changes are reverted before the next tick happens, so I wouldn't expect any of the watchers getting triggered.
// Observing and comparing the behaviour of the third and fourth watcher, makes this even more confusing.

watch(() => {
  objectRef.value.foo; // using `stringRef.value` here, yields the same result
  return Number(1);
}, callback); // not triggered by `touchRefs()`, as expected 😊

watch(() => {
  objectRef.value.foo; // using `stringRef.value` here, yields the same result
  return new Number(2);
}, callback); // triggered by `touchRefs()`, not expected 🤔

watch(() => {
  objectRef.value;
  return new Number(3);
}, callback); // not triggered by `touchRefs()`, which is confusing based on the result of the second watcher 😦

watch(() => {
  stringRef.value;
  return new Number(4);
}, callback); // triggered by `touchRefs()`, which is even more confusing 🤯

setInterval(touchRefs, 1000);
</script>

<template></template>

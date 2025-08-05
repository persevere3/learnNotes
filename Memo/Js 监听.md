``` js
function createObserver(type, target, options = {}, callback) {
  if (!target || !(target instanceof Element || target instanceof Document)) {
    console.warn(`[Observer] Invalid target for ${type}`);
    return null;
  }

  let observer;

  switch (type) {
    case 'mutation':
      observer = new MutationObserver(callback);
      observer.observe(target, {
        childList: true,
        subtree: true,
        ...options
      });
      break;

    case 'resize':
      if (typeof ResizeObserver === 'undefined') {
        console.warn('ResizeObserver is not supported in this browser');
        return null;
      }
      observer = new ResizeObserver(callback);
      observer.observe(target);
      break;

    case 'intersection':
      if (typeof IntersectionObserver === 'undefined') {
        console.warn('IntersectionObserver is not supported in this browser');
        return null;
      }
      observer = new IntersectionObserver(callback, options);
      observer.observe(target);
      break;

    case 'performance':
      if (typeof PerformanceObserver === 'undefined') {
        console.warn('PerformanceObserver is not supported in this browser');
        return null;
      }
      observer = new PerformanceObserver(callback);
      observer.observe({ entryTypes: options.entryTypes || ['navigation', 'resource'] });
      break;

    default:
      console.warn(`Unknown observer type: ${type}`);
      return null;
  }

  return observer;
}

// 通用清除方法
const observer = createObserver(...);
if (observer) {
  // 之後不需要時手動停止
  observer.disconnect();
}
```

####  MutationObserver（監控 DOM 結構）
``` js
createObserver('mutation', document.querySelector('#myTable'), {
  childList: true,
  subtree: true
}, (mutations) => {
  console.log('DOM 變動了:', mutations);
});
```

#### ResizeObserver（監控大小）
``` js
createObserver('resize', document.querySelector('#myBox'), {}, (entries) => {
  for (let entry of entries) {
    console.log('尺寸變化:', entry.contentRect);
  }
});
```
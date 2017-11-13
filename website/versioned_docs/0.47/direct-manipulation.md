---
id: version-0.47-direct-manipulation
title: direct-manipulation
original_id: direct-manipulation
---
<a id="content"></a><h1><a class="anchor" name="direct-manipulation"></a>Direct Manipulation <a class="hash-link" href="docs/direct-manipulation.html#direct-manipulation">#</a></h1><div><p>It is sometimes necessary to make changes directly to a component
without using state/props to trigger a re-render of the entire subtree.
When using React in the browser for example, you sometimes need to
directly modify a DOM node, and the same is true for views in mobile
apps. <code>setNativeProps</code> is the React Native equivalent to setting
properties directly on a DOM node.</p><blockquote><p>Use setNativeProps when frequent re-rendering creates a performance bottleneck</p><p>Direct manipulation will not be a tool that you reach for
frequently; you will typically only be using it for creating
continuous animations to avoid the overhead of rendering the component
hierarchy and reconciling many views. <code>setNativeProps</code> is imperative
and stores state in the native layer (DOM, UIView, etc.) and not
within your React components, which makes your code more difficult to
reason about. Before you use it, try to solve your problem with <code>setState</code>
and <a href="http://facebook.github.io/react/docs/advanced-performance.html#shouldcomponentupdate-in-action" target="_blank">shouldComponentUpdate</a>.</p></blockquote><h2><a class="anchor" name="setnativeprops-with-touchableopacity"></a>setNativeProps with TouchableOpacity <a class="hash-link" href="docs/direct-manipulation.html#setnativeprops-with-touchableopacity">#</a></h2><p><a href="https://github.com/facebook/react-native/blob/master/Libraries/Components/Touchable/TouchableOpacity.js" target="_blank">TouchableOpacity</a>
uses <code>setNativeProps</code> internally to update the opacity of its child
component:</p><div class="prism language-javascript"><span class="token function">setOpacityTo</span><span class="token punctuation">(</span>value<span class="token punctuation">)</span> <span class="token punctuation">{</span>
 <span class="token comment" spellcheck="true"> // Redacted: animation related code
</span>  <span class="token keyword">this</span><span class="token punctuation">.</span>refs<span class="token punctuation">[</span>CHILD_REF<span class="token punctuation">]</span><span class="token punctuation">.</span><span class="token function">setNativeProps</span><span class="token punctuation">(</span><span class="token punctuation">{</span>
    opacity<span class="token punctuation">:</span> value
  <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">,</span></div><p>This allows us to write the following code and know that the child will
have its opacity updated in response to taps, without the child having
any knowledge of that fact or requiring any changes to its implementation:</p><div class="prism language-javascript"><span class="token operator">&lt;</span>TouchableOpacity onPress<span class="token operator">=</span><span class="token punctuation">{</span><span class="token keyword">this</span><span class="token punctuation">.</span>_handlePress<span class="token punctuation">}</span><span class="token operator">&gt;</span>
  <span class="token operator">&lt;</span>View style<span class="token operator">=</span><span class="token punctuation">{</span>styles<span class="token punctuation">.</span>button<span class="token punctuation">}</span><span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>Text<span class="token operator">&gt;</span>Press me<span class="token operator">!</span><span class="token operator">&lt;</span><span class="token operator">/</span>Text<span class="token operator">&gt;</span>
  <span class="token operator">&lt;</span><span class="token operator">/</span>View<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>TouchableOpacity<span class="token operator">&gt;</span></div><p>Let's imagine that <code>setNativeProps</code> was not available. One way that we
might implement it with that constraint is to store the opacity value
in the state, then update that value whenever <code>onPress</code> is fired:</p><div class="prism language-javascript"><span class="token function">constructor</span><span class="token punctuation">(</span>props<span class="token punctuation">)</span> <span class="token punctuation">{</span>
  <span class="token keyword">super</span><span class="token punctuation">(</span>props<span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token keyword">this</span><span class="token punctuation">.</span>state <span class="token operator">=</span> <span class="token punctuation">{</span> myButtonOpacity<span class="token punctuation">:</span> <span class="token number">1</span><span class="token punctuation">,</span> <span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token function">render</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
  <span class="token keyword">return</span> <span class="token punctuation">(</span>
    <span class="token operator">&lt;</span>TouchableOpacity onPress<span class="token operator">=</span><span class="token punctuation">{</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">setState</span><span class="token punctuation">(</span><span class="token punctuation">{</span>myButtonOpacity<span class="token punctuation">:</span> <span class="token number">0.5</span><span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">}</span>
                      onPressOut<span class="token operator">=</span><span class="token punctuation">{</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">setState</span><span class="token punctuation">(</span><span class="token punctuation">{</span>myButtonOpacity<span class="token punctuation">:</span> <span class="token number">1</span><span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">}</span><span class="token operator">&gt;</span>
      <span class="token operator">&lt;</span>View style<span class="token operator">=</span><span class="token punctuation">{</span><span class="token punctuation">[</span>styles<span class="token punctuation">.</span>button<span class="token punctuation">,</span> <span class="token punctuation">{</span>opacity<span class="token punctuation">:</span> <span class="token keyword">this</span><span class="token punctuation">.</span>state<span class="token punctuation">.</span>myButtonOpacity<span class="token punctuation">}</span><span class="token punctuation">]</span><span class="token punctuation">}</span><span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>Text<span class="token operator">&gt;</span>Press me<span class="token operator">!</span><span class="token operator">&lt;</span><span class="token operator">/</span>Text<span class="token operator">&gt;</span>
      <span class="token operator">&lt;</span><span class="token operator">/</span>View<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>TouchableOpacity<span class="token operator">&gt;</span>
  <span class="token punctuation">)</span>
<span class="token punctuation">}</span></div><p>This is computationally intensive compared to the original example -
React needs to re-render the component hierarchy each time the opacity
changes, even though other properties of the view and its children
haven't changed. Usually this overhead isn't a concern but when
performing continuous animations and responding to gestures, judiciously
optimizing your components can improve your animations' fidelity.</p><p>If you look at the implementation of <code>setNativeProps</code> in
<a href="https://github.com/facebook/react/blob/master/src/renderers/native/NativeMethodsMixin.js" target="_blank">NativeMethodsMixin.js</a>
you will notice that it is a wrapper around <code>RCTUIManager.updateView</code> -
this is the exact same function call that results from re-rendering -
see <a href="https://github.com/facebook/react/blob/master/src/renderers/native/ReactNativeBaseComponent.js" target="_blank">receiveComponent in
ReactNativeBaseComponent.js</a>.</p><h2><a class="anchor" name="composite-components-and-setnativeprops"></a>Composite components and setNativeProps <a class="hash-link" href="docs/direct-manipulation.html#composite-components-and-setnativeprops">#</a></h2><p>Composite components are not backed by a native view, so you cannot call
<code>setNativeProps</code> on them. Consider this example:</p><div class="snack-player"><div class="mobile-friendly-snack" style="display:none;"><div class="prism language-javascript"><span class="token keyword">import</span> React <span class="token keyword">from</span> <span class="token string">'react'</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token punctuation">{</span> Text<span class="token punctuation">,</span> TouchableOpacity<span class="token punctuation">,</span> View <span class="token punctuation">}</span> <span class="token keyword">from</span> <span class="token string">'react-native'</span><span class="token punctuation">;</span>

<span class="token keyword">class</span> <span class="token class-name">MyButton</span> <span class="token keyword">extends</span> <span class="token class-name">React<span class="token punctuation">.</span>Component</span> <span class="token punctuation">{</span>
  <span class="token function">render</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token punctuation">(</span>
      <span class="token operator">&lt;</span>View<span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>Text<span class="token operator">&gt;</span><span class="token punctuation">{</span><span class="token keyword">this</span><span class="token punctuation">.</span>props<span class="token punctuation">.</span>label<span class="token punctuation">}</span><span class="token operator">&lt;</span><span class="token operator">/</span>Text<span class="token operator">&gt;</span>
      <span class="token operator">&lt;</span><span class="token operator">/</span>View<span class="token operator">&gt;</span>
    <span class="token punctuation">)</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token keyword">export</span> <span class="token keyword">default</span> <span class="token keyword">class</span> <span class="token class-name">App</span> <span class="token keyword">extends</span> <span class="token class-name">React<span class="token punctuation">.</span>Component</span> <span class="token punctuation">{</span>
  <span class="token function">render</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token punctuation">(</span>
      <span class="token operator">&lt;</span>TouchableOpacity<span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>MyButton label<span class="token operator">=</span><span class="token string">"Press me!"</span> <span class="token operator">/</span><span class="token operator">&gt;</span>
      <span class="token operator">&lt;</span><span class="token operator">/</span>TouchableOpacity<span class="token operator">&gt;</span>
    <span class="token punctuation">)</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span></div></div><div class="desktop-friendly-snack" style="margin-top:15px;margin-bottom:15px;"><div data-snack-name="setNativeProps with Composite Components" data-snack-description="Example usage" data-snack-code="import%20React%20from%20'react'%3B%0Aimport%20%7B%20Text%2C%20TouchableOpacity%2C%20View%20%7D%20from%20'react-native'%3B%0A%0Aclass%20MyButton%20extends%20React.Component%20%7B%0A%20%20render()%20%7B%0A%20%20%20%20return%20(%0A%20%20%20%20%20%20%3CView%3E%0A%20%20%20%20%20%20%20%20%3CText%3E%7Bthis.props.label%7D%3C%2FText%3E%0A%20%20%20%20%20%20%3C%2FView%3E%0A%20%20%20%20)%0A%20%20%7D%0A%7D%0A%0Aexport%20default%20class%20App%20extends%20React.Component%20%7B%0A%20%20render()%20%7B%0A%20%20%20%20return%20(%0A%20%20%20%20%20%20%3CTouchableOpacity%3E%0A%20%20%20%20%20%20%20%20%3CMyButton%20label%3D%22Press%20me!%22%20%2F%3E%0A%20%20%20%20%20%20%3C%2FTouchableOpacity%3E%0A%20%20%20%20)%0A%20%20%7D%0A%7D" data-snack-platform="ios" data-snack-preview="true" data-snack-sdk-version="16.0.0" style="overflow:hidden;background:#fafafa;border:1px solid rgba(0,0,0,.16);border-radius:4px;height:514px;width:880px;"></div></div></div><p>If you run this you will immediately see this error: <code>Touchable child
must either be native or forward setNativeProps to a native component</code>.
This occurs because <code>MyButton</code> isn't directly backed by a native view
whose opacity should be set. You can think about it like this: if you
define a component with <code>createReactClass</code> you would not expect to be
able to set a style prop on it and have that work - you would need to
pass the style prop down to a child, unless you are wrapping a native
component. Similarly, we are going to forward <code>setNativeProps</code> to a
native-backed child component.</p><h4><a class="anchor" name="forward-setnativeprops-to-a-child"></a>Forward setNativeProps to a child <a class="hash-link" href="docs/direct-manipulation.html#forward-setnativeprops-to-a-child">#</a></h4><p>All we need to do is provide a <code>setNativeProps</code> method on our component
that calls <code>setNativeProps</code> on the appropriate child with the given
arguments.</p><div class="snack-player"><div class="mobile-friendly-snack" style="display:none;"><div class="prism language-javascript"><span class="token keyword">import</span> React <span class="token keyword">from</span> <span class="token string">'react'</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token punctuation">{</span> Text<span class="token punctuation">,</span> TouchableOpacity<span class="token punctuation">,</span> View <span class="token punctuation">}</span> <span class="token keyword">from</span> <span class="token string">'react-native'</span><span class="token punctuation">;</span>

<span class="token keyword">class</span> <span class="token class-name">MyButton</span> <span class="token keyword">extends</span> <span class="token class-name">React<span class="token punctuation">.</span>Component</span> <span class="token punctuation">{</span>
  setNativeProps <span class="token operator">=</span> <span class="token punctuation">(</span>nativeProps<span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token punctuation">{</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span>_root<span class="token punctuation">.</span><span class="token function">setNativeProps</span><span class="token punctuation">(</span>nativeProps<span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token function">render</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token punctuation">(</span>
      <span class="token operator">&lt;</span>View ref<span class="token operator">=</span><span class="token punctuation">{</span>component <span class="token operator">=&gt;</span> <span class="token keyword">this</span><span class="token punctuation">.</span>_root <span class="token operator">=</span> component<span class="token punctuation">}</span> <span class="token punctuation">{</span><span class="token operator">...</span><span class="token keyword">this</span><span class="token punctuation">.</span>props<span class="token punctuation">}</span><span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>Text<span class="token operator">&gt;</span><span class="token punctuation">{</span><span class="token keyword">this</span><span class="token punctuation">.</span>props<span class="token punctuation">.</span>label<span class="token punctuation">}</span><span class="token operator">&lt;</span><span class="token operator">/</span>Text<span class="token operator">&gt;</span>
      <span class="token operator">&lt;</span><span class="token operator">/</span>View<span class="token operator">&gt;</span>
    <span class="token punctuation">)</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token keyword">export</span> <span class="token keyword">default</span> <span class="token keyword">class</span> <span class="token class-name">App</span> <span class="token keyword">extends</span> <span class="token class-name">React<span class="token punctuation">.</span>Component</span> <span class="token punctuation">{</span>
  <span class="token function">render</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token punctuation">(</span>
      <span class="token operator">&lt;</span>TouchableOpacity<span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>MyButton label<span class="token operator">=</span><span class="token string">"Press me!"</span> <span class="token operator">/</span><span class="token operator">&gt;</span>
      <span class="token operator">&lt;</span><span class="token operator">/</span>TouchableOpacity<span class="token operator">&gt;</span>
    <span class="token punctuation">)</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span></div></div><div class="desktop-friendly-snack" style="margin-top:15px;margin-bottom:15px;"><div data-snack-name="Forwarding setNativeProps" data-snack-description="Example usage" data-snack-code="import%20React%20from%20'react'%3B%0Aimport%20%7B%20Text%2C%20TouchableOpacity%2C%20View%20%7D%20from%20'react-native'%3B%0A%0Aclass%20MyButton%20extends%20React.Component%20%7B%0A%20%20setNativeProps%20%3D%20(nativeProps)%20%3D%3E%20%7B%0A%20%20%20%20this._root.setNativeProps(nativeProps)%3B%0A%20%20%7D%0A%0A%20%20render()%20%7B%0A%20%20%20%20return%20(%0A%20%20%20%20%20%20%3CView%20ref%3D%7Bcomponent%20%3D%3E%20this._root%20%3D%20component%7D%20%7B...this.props%7D%3E%0A%20%20%20%20%20%20%20%20%3CText%3E%7Bthis.props.label%7D%3C%2FText%3E%0A%20%20%20%20%20%20%3C%2FView%3E%0A%20%20%20%20)%0A%20%20%7D%0A%7D%0A%0Aexport%20default%20class%20App%20extends%20React.Component%20%7B%0A%20%20render()%20%7B%0A%20%20%20%20return%20(%0A%20%20%20%20%20%20%3CTouchableOpacity%3E%0A%20%20%20%20%20%20%20%20%3CMyButton%20label%3D%22Press%20me!%22%20%2F%3E%0A%20%20%20%20%20%20%3C%2FTouchableOpacity%3E%0A%20%20%20%20)%0A%20%20%7D%0A%7D" data-snack-platform="ios" data-snack-preview="true" data-snack-sdk-version="16.0.0" style="overflow:hidden;background:#fafafa;border:1px solid rgba(0,0,0,.16);border-radius:4px;height:514px;width:880px;"></div></div></div><p>You can now use <code>MyButton</code> inside of <code>TouchableOpacity</code>! A sidenote for
clarity: we used the <a href="https://facebook.github.io/react/docs/more-about-refs.html#the-ref-callback-attribute" target="_blank">ref callback</a> syntax here, rather than the traditional string-based ref.</p><p>You may have noticed that we passed all of the props down to the child
view using <code>{...this.props}</code>. The reason for this is that
<code>TouchableOpacity</code> is actually a composite component, and so in addition
to depending on <code>setNativeProps</code> on its child, it also requires that the
child perform touch handling. To do this, it passes on <a href="docs/view.html#onmoveshouldsetresponder" target="_blank">various
props</a>
that call back to the <code>TouchableOpacity</code> component.
<code>TouchableHighlight</code>, in contrast, is backed by a native view and only
requires that we implement <code>setNativeProps</code>.</p><h2><a class="anchor" name="setnativeprops-to-clear-textinput-value"></a>setNativeProps to clear TextInput value <a class="hash-link" href="docs/direct-manipulation.html#setnativeprops-to-clear-textinput-value">#</a></h2><p>Another very common use case of <code>setNativeProps</code> is to clear the value
of a TextInput. The <code>controlled</code> prop of TextInput can sometimes drop
characters when the <code>bufferDelay</code> is low and the user types very
quickly. Some developers prefer to skip this prop entirely and instead
use <code>setNativeProps</code> to directly manipulate the TextInput value when
necessary. For example, the following code demonstrates clearing the
input when you tap a button:</p><div class="snack-player"><div class="mobile-friendly-snack" style="display:none;"><div class="prism language-javascript"><span class="token keyword">import</span> React <span class="token keyword">from</span> <span class="token string">'react'</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token punctuation">{</span> TextInput<span class="token punctuation">,</span> Text<span class="token punctuation">,</span> TouchableOpacity<span class="token punctuation">,</span> View <span class="token punctuation">}</span> <span class="token keyword">from</span> <span class="token string">'react-native'</span><span class="token punctuation">;</span>

<span class="token keyword">export</span> <span class="token keyword">default</span> <span class="token keyword">class</span> <span class="token class-name">App</span> <span class="token keyword">extends</span> <span class="token class-name">React<span class="token punctuation">.</span>Component</span> <span class="token punctuation">{</span>
  clearText <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token punctuation">{</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span>_textInput<span class="token punctuation">.</span><span class="token function">setNativeProps</span><span class="token punctuation">(</span><span class="token punctuation">{</span>text<span class="token punctuation">:</span> <span class="token string">''</span><span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token function">render</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token punctuation">(</span>
      <span class="token operator">&lt;</span>View style<span class="token operator">=</span><span class="token punctuation">{</span><span class="token punctuation">{</span>flex<span class="token punctuation">:</span> <span class="token number">1</span><span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>TextInput
          ref<span class="token operator">=</span><span class="token punctuation">{</span>component <span class="token operator">=&gt;</span> <span class="token keyword">this</span><span class="token punctuation">.</span>_textInput <span class="token operator">=</span> component<span class="token punctuation">}</span>
          style<span class="token operator">=</span><span class="token punctuation">{</span><span class="token punctuation">{</span>height<span class="token punctuation">:</span> <span class="token number">50</span><span class="token punctuation">,</span> flex<span class="token punctuation">:</span> <span class="token number">1</span><span class="token punctuation">,</span> marginHorizontal<span class="token punctuation">:</span> <span class="token number">20</span><span class="token punctuation">,</span> borderWidth<span class="token punctuation">:</span> <span class="token number">1</span><span class="token punctuation">,</span> borderColor<span class="token punctuation">:</span> <span class="token string">'#ccc'</span><span class="token punctuation">}</span><span class="token punctuation">}</span>
        <span class="token operator">/</span><span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>TouchableOpacity onPress<span class="token operator">=</span><span class="token punctuation">{</span><span class="token keyword">this</span><span class="token punctuation">.</span>clearText<span class="token punctuation">}</span><span class="token operator">&gt;</span>
          <span class="token operator">&lt;</span>Text<span class="token operator">&gt;</span>Clear text<span class="token operator">&lt;</span><span class="token operator">/</span>Text<span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span><span class="token operator">/</span>TouchableOpacity<span class="token operator">&gt;</span>
      <span class="token operator">&lt;</span><span class="token operator">/</span>View<span class="token operator">&gt;</span>
    <span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span></div></div><div class="desktop-friendly-snack" style="margin-top:15px;margin-bottom:15px;"><div data-snack-name="Clear text" data-snack-description="Example usage" data-snack-code="import%20React%20from%20'react'%3B%0Aimport%20%7B%20TextInput%2C%20Text%2C%20TouchableOpacity%2C%20View%20%7D%20from%20'react-native'%3B%0A%0Aexport%20default%20class%20App%20extends%20React.Component%20%7B%0A%20%20clearText%20%3D%20()%20%3D%3E%20%7B%0A%20%20%20%20this._textInput.setNativeProps(%7Btext%3A%20''%7D)%3B%0A%20%20%7D%0A%0A%20%20render()%20%7B%0A%20%20%20%20return%20(%0A%20%20%20%20%20%20%3CView%20style%3D%7B%7Bflex%3A%201%7D%7D%3E%0A%20%20%20%20%20%20%20%20%3CTextInput%0A%20%20%20%20%20%20%20%20%20%20ref%3D%7Bcomponent%20%3D%3E%20this._textInput%20%3D%20component%7D%0A%20%20%20%20%20%20%20%20%20%20style%3D%7B%7Bheight%3A%2050%2C%20flex%3A%201%2C%20marginHorizontal%3A%2020%2C%20borderWidth%3A%201%2C%20borderColor%3A%20'%23ccc'%7D%7D%0A%20%20%20%20%20%20%20%20%2F%3E%0A%20%20%20%20%20%20%20%20%3CTouchableOpacity%20onPress%3D%7Bthis.clearText%7D%3E%0A%20%20%20%20%20%20%20%20%20%20%3CText%3EClear%20text%3C%2FText%3E%0A%20%20%20%20%20%20%20%20%3C%2FTouchableOpacity%3E%0A%20%20%20%20%20%20%3C%2FView%3E%0A%20%20%20%20)%3B%0A%20%20%7D%0A%7D" data-snack-platform="ios" data-snack-preview="true" data-snack-sdk-version="16.0.0" style="overflow:hidden;background:#fafafa;border:1px solid rgba(0,0,0,.16);border-radius:4px;height:514px;width:880px;"></div></div></div><h2><a class="anchor" name="avoiding-conflicts-with-the-render-function"></a>Avoiding conflicts with the render function <a class="hash-link" href="docs/direct-manipulation.html#avoiding-conflicts-with-the-render-function">#</a></h2><p>If you update a property that is also managed by the render function,
you might end up with some unpredictable and confusing bugs because
anytime the component re-renders and that property changes, whatever
value was previously set from <code>setNativeProps</code> will be completely
ignored and overridden.</p><h2><a class="anchor" name="setnativeprops-shouldcomponentupdate"></a>setNativeProps &amp; shouldComponentUpdate <a class="hash-link" href="docs/direct-manipulation.html#setnativeprops-shouldcomponentupdate">#</a></h2><p>By <a href="https://facebook.github.io/react/docs/advanced-performance.html#avoiding-reconciling-the-dom" target="_blank">intelligently applying
<code>shouldComponentUpdate</code></a>
you can avoid the unnecessary overhead involved in reconciling unchanged
component subtrees, to the point where it may be performant enough to
use <code>setState</code> instead of <code>setNativeProps</code>.</p><h2><a class="anchor" name="other-native-methods"></a>Other native methods <a class="hash-link" href="docs/direct-manipulation.html#other-native-methods">#</a></h2><p>The methods described here are available on most of the default components provided by React Native. Note, however, that they are <em>not</em> available on composite components that aren't directly backed by a native view. This will generally include most components that you define in your own app.</p><h3><a class="anchor" name="measure-callback"></a>measure(callback) <a class="hash-link" href="docs/direct-manipulation.html#measure-callback">#</a></h3><p>Determines the location on screen, width, and height of the given view and returns the values via an async callback. If successful, the callback will be called with the following arguments:</p><ul><li>x</li><li>y</li><li>width</li><li>height</li><li>pageX</li><li>pageY</li></ul><p>Note that these measurements are not available until after the rendering has been completed in native. If you need the measurements as soon as possible, consider using the <a href="docs/view.html#onlayout" target="_blank"><code>onLayout</code> prop</a> instead.</p><h3><a class="anchor" name="measureinwindow-callback"></a>measureInWindow(callback) <a class="hash-link" href="docs/direct-manipulation.html#measureinwindow-callback">#</a></h3><p>Determines the location of the given view in the window and returns the values via an async callback. If the React root view is embedded in another native view, this will give you the absolute coordinates. If successful, the callback will be called with the following arguments:</p><ul><li>x</li><li>y</li><li>width</li><li>height</li></ul><h3><a class="anchor" name="measurelayout-relativetonativenode-onsuccess-onfail"></a>measureLayout(relativeToNativeNode, onSuccess, onFail) <a class="hash-link" href="docs/direct-manipulation.html#measurelayout-relativetonativenode-onsuccess-onfail">#</a></h3><p>Like <code>measure()</code>, but measures the view relative an ancestor, specified as <code>relativeToNativeNode</code>. This means that the returned x, y are relative to the origin x, y of the ancestor view.</p><p>As always, to obtain a native node handle for a component, you can use <code>ReactNative.findNodeHandle(component)</code>.</p><h3><a class="anchor" name="focus"></a>focus() <a class="hash-link" href="docs/direct-manipulation.html#focus">#</a></h3><p>Requests focus for the given input or view. The exact behavior triggered will depend on the platform and type of view.</p><h3><a class="anchor" name="blur"></a>blur() <a class="hash-link" href="docs/direct-manipulation.html#blur">#</a></h3><p>Removes focus from an input or view. This is the opposite of <code>focus()</code>.</p></div><div class="docs-prevnext"><a class="docs-prev btn" href="docs/javascript-environment.html#content">← Previous</a><a class="docs-next btn" href="docs/colors.html#content">Continue Reading →</a></div><p class="edit-page-block"><a target="_blank" href="https://github.com/facebook/react-native/blob/master/docs/DirectManipulation.md">Improve this page</a> by sending a pull request!</p>
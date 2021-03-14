---
published: false
---
Recently i faced an issue where while accessing the external url for the application and trying to login it is failing to login with "Un authorized error". and i observed that it is happening only with the external url and working as expected with the internal url.<br/>

As a first step of debugging(like every other person) i have taken the har report from the client machine and observed that during the login it is trying to perform the "POST" request and the "POST" request is failed with the below error:<br/>
```java
 The request has not been applied because it lacks valid authentication credentials for the target resource
 ```
 <br/>
From the above error we got to know that somehow the credentials sent by the client are not the same that server has received while trying to login with external url(external url means before connecting to actual server it passes through some of the load balancer nodes).
<br/>

But how will i confirm that credentials received by server are correct to make sure that my assumptions are correct. initially i have explored the options on installing the fiddler on the loadbalancer machines to verify if the credentials are being modified by any of the loadbalancer machines but as expected the loadbalancer machines are not accessible by me and i dont have the control on the loadbalancer machines. But after that i taught if my application(POST method) could print the values it received but i can modify the jar files as they are already deployed in the production environments.
<br/><br/>

Then i taught of the javaagents which can be attached while starting the Process so that when the "POST" method is called during the login it would print the "request" argument which is part of the "POST" method.
<br/>

Below are the steps followed for developing the agent for printing the argument values for the function:<br/>
1.when we are developing the agent we can attach the agent to the application statically or dynamically while static attachment we use the "premain" method and for the dynamic attachment we use the agentmain method(these methods are similar to the main method in normal java application).<br/>
2.then we have defined the transformClass method and transform methods.<br/>
3.and then implemented the ClassFileTransformer interface and have created the body for the "public byte[] transform()" method.<br/>

<br/>

Below is the Static_Agent class where the premain,transformClass and the transform method in the "transformClass" method we are verifying if the class and the method that we are interested in are loaded/called and then in the "transform" method  have created a new "Transformer which implemented the ClassFileTransformer interface".<br/>

```java
package first_agent;

import java.lang.instrument.Instrumentation;
import first_agent.Transformer;
public class Static_Agent {

	public static void preMain(String args,Instrumentation inst) {
		String[] tokens=args.split(";");
		
		String className=tokens[0];
		String methodName=tokens[1];
		
		System.out.println("we are instrumenting"+className+" and the "+methodName+" method");
		
		transformClass(className,methodName,inst);
	}
	public static void transformClass(String className,String methodName,Instrumentation inst) {
		 Class<?> targetCls = null;
	     ClassLoader targetClassLoader = null;
	     
	     try {
	    	 targetCls=Class.forName(className);
	    	 targetClassLoader=targetCls.getClassLoader();
	    	 System.out.println(targetClassLoader);
             transform(targetCls, methodName, targetClassLoader, inst);
	     }
	     catch(Exception e) {
	    	 e.printStackTrace();
	    	 return;
	     }
	     
	     for(Class<?> clazz: inst.getAllLoadedClasses()) {
	            if(clazz.getName().equals(className)) {
	                targetCls = clazz;
	                targetClassLoader = targetCls.getClassLoader();
	                transform(targetCls, methodName, targetClassLoader, inst);
	                return;
	            }
	        }
	        throw new RuntimeException("Failed to find class [" + className + "]");
	}
	
	public static void transform(Class<?> clazz,String methodName,ClassLoader targetClassLoader,Instrumentation inst) {
		Transformer dt=new Transformer(clazz.getName(),methodName,targetClassLoader);
		inst.addTransformer(dt,true);
		try {
			inst.retransformClasses(clazz);
		}
		catch(Exception e) {
			e.printStackTrace();
		}
	}
}
```
<br/><br/>

Below is the Transfromer class where we have implemented the ClassFileTransformer and modified the byte code of the method:<br/>
```java
package first_agent;

import java.lang.instrument.ClassFileTransformer;
import java.lang.instrument.IllegalClassFormatException;
import java.security.ProtectionDomain;
import javassist.ClassPool;
import javassist.CtClass;
import javassist.CtMethod;
public class Transformer implements ClassFileTransformer {
  private String targetClassName;
  private ClassLoader targetClassLoader;
  private String targetMethodName;
  
  
  public Transformer(String targetClassName, String targetMethodName, ClassLoader targetClassLoader) {
      this.targetClassName = targetClassName;
      this.targetClassLoader = targetClassLoader;
      this.targetMethodName = targetMethodName;
  }

public byte[] transform(ClassLoader loader, String className, Class<?> classBeingRedefined,
            ProtectionDomain protectionDomain, byte[] classfileBuffer) throws IllegalClassFormatException{
	  byte[] byteCode=classfileBuffer;
      String finalTargetClassName = this.targetClassName.replaceAll("\\.", "/");
      if (!className.equals(finalTargetClassName)) {
          return byteCode;
      }
      if (className.equals(finalTargetClassName) && loader.equals(targetClassLoader)) {
    	  System.out.println("changing the byte code of "+finalTargetClassName);
    	  try {
    		  ClassPool cp=ClassPool.getDefault();
    		  CtClass cc = cp.get(targetClassName);
              CtMethod m = cc.getDeclaredMethod(targetMethodName);
              m.insertBefore("System.out.println($1);System.out.println($2);");
              cc.detach();
              byteCode=cc.toBytecode();
              }
    	  catch(Exception e) {
    		  e.printStackTrace();
    	  }
      }
	  return byteCode;

  }
}
/java/bin/java -javaagent:agent.jar="Test;sum" -cp "/root/javaassist*.jar:Test.jar" Test
```

 

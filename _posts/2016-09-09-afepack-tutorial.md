---
published: true
title: AFEPack Tutorial
layout: post
author:
category: tutorial 
tags: [AFEPack]
comments: true 
---

---


AFEPack 的基本使用方法，我们通过下面的求解泊松方 程的程序来具体讲解。假设我们现在需要在区 域 $ \Omega = [0, 1]\times[0, 1] $ 上来求解一个狄氏边值问题的泊松方程

\[
-\Delta u = f, \qquad u |_{\partial \Omega} = u_b.
\]

作为有限元程序，第一个问题就是要对区域进 行剖分，得到网格的数据。 我们这里使用一个简单的二维三角形网格剖分 软件EasyMesh来去区域进行剖分。 EasyMesh 有一个一页纸的说明书，请读者自己去阅读它 。EasyMesh 需要用户手工写一个对区域进行描述的文件作 为输入文件，我们使用的文件名为 D.d，其内容如下：

4 # 区域的顶点的个数 #
0: 0.0 0.0 0.05 1
1: 1.0 0.0 0.05 1
2: 1.0 1.0 0.05 1
3: 0.0 1.0 0.05 1

4 # 区域的边界上边的条数 #
0: 0 1 1
1: 1 2 1
2: 2 3 1
3: 3 4 1



其中前面一个部分描述区域中的顶点， 共有4个，然后每一行描述一个顶点的信息， 其意义为

顶点的序号:    x坐标    y坐标    剖分密度h    材料标识



后面一个部分则描述区域的边界上的边的条数 ，共有4条，然后每一行描述一条
边的信息，其意义为

边的序号:    起始顶点序号    结束顶点序号    材料标识



以这个文件的主文件名 D 作为输入，运行 easymesh D，即可获得网格数据， 存储在三个不同的文件中， 名为 D.n，D.s，D.e，存储的分别是节点、边和单元的 信息。EasyMesh 的说明中有对这些文件格式的详细说明，而 AFEPack 也提供了对这种数据格式的文件读入的接口。

在得到了网格数据以后，我们即可以开始对程 序进行说明。我们的整个程序的原文和详细的 说明如下：

/**
 * 下面这些包含的头文件都是属于 AFEPack 的。
 */
#include <AFEPack/AMGSolver.h>        /// 代数多重网格求解器
#include <AFEPack/TemplateElement.h>  /// 参考单元
#include <AFEPack/FEMSpace.h>         /// 有限元空间
#include <AFEPack/Operator.h>         /// 算子
#include <AFEPack/BilinearOperator.h> /// 双线性算子
#include <AFEPack/Functional.h>       /// 泛函
#include <AFEPack/EasyMesh.h>         /// EasyMesh 接口

#define DIM 2                /**< 区域的维数 */
#define PI (4.0*atan(1.0))   /**< 常数 \pi   */

/**
 * 精确解 u 的表达式
 *
 * @param p 区域上的点的坐标: x = p[0], y = p[1]
 *
 * @return u(x, y)
 */
double _u_(const double * p)
{
  return sin(PI*p[0]) * sin(2*PI*p[1]);
}

/**
 *  右端项函数 f 的表达式
 *
 * @param p 区域上的点的坐标: x = p[0], y = p[1]
 *
 * @return f(x, y)
 */
double _f_(const double * p)
{
  return 5*PI*PI*_u_(p);
}

/**
 * 主函数：程序在运行的时候，我们要求有一个命令行参数，即为网格数据文
 *         件名 D。
 *
 * @param argc 命令行参数个数
 * @param argv 命令行参数列表
 *
 * @return 返回 0
 */
int main(int argc, char * argv[])
{
  /**
   * AFEPack 中的 EasyMesh 类能够读入 EasyMesh 产生的数据文件的接口，
   * 它本身是由网格类 Mesh 派生出来的，可以当成一个网格使用
   */
  EasyMesh mesh;
  mesh.readData(argv[1]); /// 以第一个命令行参数为文件名，读入网格数据

  /**
   * 下面这个段落是为有限元空间准备参考单元的数据。AFEPack 将参考单元
   * 的数据分成为四个不同的信息，包括：
   *
   *   - 参考单元的几何信息
   *   - 参考单元到网格中的单元的坐标变换
   *   - 单元上的自由度分布
   *   - 单元上的基函数
   *
   * 使用这几个信息反复组合，可以得到很多种不同的参考单元。这四个信息
   * 的管理使用了四个不同的类，名为 TemplateGeometry, CoordTransform,
   * TemplateDOF 和 BasisFunctionAdmin。这四个类都可以从数据文件中读入
   * 信息来构造其自身的结构，而对于很多常用的单元，这些数据文件都已经
   * 准备在了 AFEPack 的 template 子目录下。这些数据文件的数据格式在
   * AFEPack 的文档之中有比较详细的说明。为了能够使得 AFEPack 顺利的找
   * 到这些数据文件，我们需要设置环境变量 AFEPACK_TEMPLATE_PATH。比如
   * 对于我们这个问题的计算， 我们需要设置(假设 bash)
   *
   * <pre>
   *   $ export AFEPACK_TEMPLATE_PATH=$AFEPACK_PATH/template/triangle
   * </pre>
   *
   * 其中 AFEPACK_PATH 假设为 AFEPack 的安装路径。
   */

  /**
   * 参考单元的几何信息：其中模板参数 DIM 为参考单元的维数，对于我们现 
   * 在的三角形来说就是 2。 参考单元的几何信息中包括参考单元的几何结构， 
   * 以及进行数值积分的时候的一系列积分公式的信息。
   *
   */
  TemplateGeometry<DIM> triangle_template_geometry;
  triangle_template_geometry.readData("triangle.tmp_geo");

  /**
   * 从参考单元到网格中的单元的坐标变换：这个类有两个模板参数，分别为
   * 参考单元的维数和网格中单元的维数。这两个数可能是不一样的，比如从
   * 标准三角形到球面三角形的坐标变换。这个类中提供了将参考单元中的点
   * 和实际网格单元中的点相互进行变换以及变换的雅可比行列式的计算方法。
   */
  CoordTransform<DIM,DIM> triangle_coord_transform;
  triangle_coord_transform.readData("triangle.crd_trs");

  /**
   * 自由度分布指定了在单元的每个构件几何体上分布的自由度的个数，因此
   * 它需要在知道了参考单元的几何构型的情况下进行初始化。
   */
  TemplateDOF<DIM> triangle_template_dof(triangle_template_geometry);
  triangle_template_dof.readData("triangle.1.tmp_dof");

  /**
   * 类 BasisFunctionAdmin 事实上管理着一组基函数，每个基函数为指定自
   * 己依赖的几何体，在参考单元上的插值点，一组称为"基函数识别协议"的
   * 信息，以及每个基函数的函数值和函数梯度的计算方法。
   */
  BasisFunctionAdmin<double,DIM,DIM> triangle_basis_function(triangle_template_dof);
  triangle_basis_function.readData("triangle.1.bas_fun");

  /**
   * 将上面的这四个信息组合到类 TemplateElement 中，就得到了一个完整的
   * 参考单元。而建立有限元空间需要一个网格和一组参考单元，现在我们只
   * 使用一个参考单元。
   */
  std::vector<TemplateElement<double,DIM,DIM> > template_element(1);
  template_element[0].reinit(triangle_template_geometry,
                             triangle_template_dof,
                             triangle_coord_transform,
                             triangle_basis_function);

  /**
   * 使用我们读入的 EasyMesh 得到的网格，和刚刚创建的参考单元组来初始
   * 化有限元空间。
   */
  FEMSpace<double,DIM> fem_space(mesh, template_element);
        
  /**
   * 我们先取出网格中的单元的个数，然后据此为有限元空间中的单元保留网
   * 格中的几何体单元同样数量的空间。此后，我们使得有限元空间中的每个
   * 单元是根据网格中的相应序号的几何体单元映射上第零号参考单元得到的。
   */
  int n_element = mesh.n_geometry(DIM);
  fem_space.element().resize(n_element);
  for (int i = 0;i < n_element;i ++) {
    fem_space.element(i).reinit(fem_space,i,0);
  }

  /**
   * 下面三行分别建立有限元空间中的单元的内部数据结构，然后在整个网格
   * 上分布自由度以及为所有的自由度确定材料标识。自此，整个有限元空间
   * 的所有数据就都准备好了。
   */
  fem_space.buildElement();
  fem_space.buildDof();
  fem_space.buildDofBoundaryMark();

  /**
   * 在上面建立的这个有限元空间上，我们计算一个刚度矩阵，就是由元素
   *
   * \f[
   *    a_{ij} = \int_\Omega \nabla \phi^i \cdot \nabla \phi^j dx
   * \f]
   *
   * 所构成的矩阵。由于这是常用矩阵，因此库中间有准备这个矩阵。建立这
   * 个矩阵，我们先使用上面的有限元空间构造这个矩阵的对象，然后设置计
   * 算矩阵时候使用的数值积分公式的代数精度的阶数。最后，调用函数
   * build 即可将矩阵计算出来。
   */
  StiffMatrix<DIM,double> stiff_matrix(fem_space);
  stiff_matrix.algebricAccuracy() = 3;
  stiff_matrix.build();

  /**
   * 有限元函数 u_h 用来逼近解函数 u
   */
  FEMFunction<double,DIM> u_h(fem_space);

  /**
   * 向量 f_h 用来计算右端项 f 在离散过后得到的向量，其元素为
   *
   * \f[
   *    f_i = \int_\Omega f \phi^i dx
   * \f]
   *
   * 函数 Operator::L2Discretize 帮助我们完成这个向量的计算。
   */
  Vector<double> f_h;
  Operator::L2Discretize(&_f_, fem_space, f_h, 3);

  /**
   * 下面这段是使用上狄氏边值条件：我们为材料标识为 1 的自由度使用函数
   * 表达式计算了其函数值，从而应用上了想要的边值条件。我们通过直接修
   * 改获得的线性系统中的稀疏矩阵和右端项来达成这样的结果。
   */
  BoundaryFunction<double,DIM> boundary(BoundaryConditionInfo::DIRICHLET,
                                        1, &_u_);
  BoundaryConditionAdmin<double,DIM> boundary_admin(fem_space);
  boundary_admin.add(boundary);
  boundary_admin.apply(stiff_matrix, u_h, f_h);

  /**
   * 调用 AFEPack 中的代数多重网格求解器，求解线性系统
   */
  AMGSolver solver(stiff_matrix);
  solver.solve(u_h, f_h);      

  /**
   * 将解输出到一个数据文件中。我们这里使用了 Open Data Explorer 的数
   * 据文件格式，输出的数据可以使用该软件来做可视化。
   */
  u_h.writeOpenDXData("u.dx");

  /**
   * 由于这个问题有精确解，我们计算一下数值解和精确解的 L^2 误差。
   */
  double error = Functional::L2Error(u_h, FunctionFunction<double>(&_u_), 3);
  std::cerr << "\nL2 error = " << error << std::endl;

  return 0;
}



这个程序是使用 AFEPack 进行程序设计的基本套路，虽然其中基本上没 有用户输入的关于计算方面的内容，但是很多 问题都可以通过对这个程序进行不多的修改就 
能完成计算f

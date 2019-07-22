# tinyLM
A tiny implementation of a Levenberg-Marquardt non-linear least-squares solver.

Based n the eigen 

## Install

To install this single-file header library, just download the tinyLM.hpp file, and include it somewhere appropriate in your project.

Everything is wrapped nicely into a tinyLM namespace, so you can safely include it anywhere.
I would suggest making yourself a single-file wrapper any incorporate as an invisible dependency in your project, for example, like so:

  ```c++
  // file: LM_wrapper.hpp
  #include "tinyLM.hpp"
  
  namespace MyAwesomeProject {
    using tinyLM;
  }
  // end-of-file
  ```

It is really as easy as that!

## Usage

  ```c++
  // file: problem.hpp
  #include "tinyLM.hpp"
  
  #include <tuple>
  #include <iostream>
  
  // Include some project-specific headers to define load_data()!!
  
  function eval_sphere_fit(const Eigen::Vector4d& x) {
    auto radius = x[0];
    auto center = x.tail<3>();
    
    Eigen::ArrayXXd data_centered = data.colwise() - center;
    Eigen::ArrayXd data_radius = data_centered.colwise().norm();
    Eigen::ArrayXd residuals = data_radius - radius;
 
    Eigen::Matrix<double, Eigen::Dynamic, 4> Jacobian(data.cols(), 4);
    Jacobian.col(0).array() = -1;
    Jacobian.col(1) = data_centered.row(0).array() / data_radius.array();
    Jacobian.col(2) = data_centered.row(1).array() / data_radius.array();
    Jacobian.col(3) = data_centered.row(2).array() / data_radius.array();
    
    return std::make_tuple(residuals, Jacobian);
  }
  
  int main() {
    
    Eigen::ArrayXXd data = load_data();
    auto [x, LM_state] = tinyLM::LM(eval_sphere_fit, Eigen::Vector4d::Zero())

    double radius = x[0];
    Eigen::Vector3d center = x.tail<3>();

    std::cout << "Radius: " << radius << std::endl;
    std::cout << "Center: " << center.transpose() << std::endl;

    std::cout << LM_state << std::endl;
  }
  
  // end-of-file
  ```

## What licence?

For now, I will retain all rights until I find the right licence.
I expect the licence to be rather permissive, but cannot yet say how much.
